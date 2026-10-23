#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define NAME_CAPACITY 50
#define ROLE_MEMBER 0
#define ROLE_PRESIDENT 1
#define ROLE_SECRETARY 2
#define REGISTRY_COUNT 2

 typedef struct ClubMember {
     long prnValue;
    char fullName[NAME_CAPACITY];
     int roleTag;
    struct ClubMember *nextLink;
} ClubMember;

static ClubMember *clubRegistry[REGISTRY_COUNT] = {NULL, NULL};

ClubMember *buildMemberNode(long prnValue, const char *fullName, int roleTag)
{
     ClubMember *freshNode = (ClubMember *)malloc(sizeof(ClubMember));
    if (freshNode == NULL) {
        printf("Memory allocation failed.\n");
        return NULL;
    }
      freshNode->prnValue = prnValue;
    strncpy(freshNode->fullName, fullName, NAME_CAPACITY - 1);
    freshNode->fullName[NAME_CAPACITY - 1] = '\0';
    freshNode->roleTag = roleTag;
      freshNode->nextLink = NULL;
    return freshNode;
}

const char *describeRole(int roleTag)
{
      if (roleTag == ROLE_PRESIDENT) return "President";
      if (roleTag == ROLE_SECRETARY) return "Secretary";
      return "Member";
}

ClubMember *locateByPrn(ClubMember *listHead, long targetPrn)
{
    ClubMember *walkerNode = listHead;
    while (walkerNode != NULL && walkerNode->prnValue != targetPrn)
        walkerNode = walkerNode->nextLink;
    return walkerNode;
}

ClubMember *locateByRole(ClubMember *listHead, int targetRole)
{
    ClubMember *walkerNode = listHead;
    while (walkerNode != NULL && walkerNode->roleTag != targetRole)
        walkerNode = walkerNode->nextLink;
    return walkerNode;
}

ClubMember *locateTail(ClubMember *listHead)
{
    ClubMember *walkerNode = listHead;
    while (walkerNode != NULL && walkerNode->nextLink != NULL)
        walkerNode = walkerNode->nextLink;
    return walkerNode;
}

int tallyMembers(ClubMember *listHead)
{
       int runningTotal = 0;
      ClubMember *walkerNode = listHead;
     while (walkerNode != NULL) {
        runningTotal++;
        walkerNode = walkerNode->nextLink;
    }
    return runningTotal;
}

void showRoster(ClubMember *listHead)
{
        ClubMember *walkerNode = listHead;
    int serialNumber = 1;
    if (listHead == NULL) {
        printf("No students registered.\n");
        return;
    }
      printf("\n%-5s %-15s %-30s %-10s\n", "No.", "PRN", "Name", "Role");
        printf("---------------------------------------------------------------\n");
    while (walkerNode != NULL) {
        printf("%-5d %-15ld %-30s %-10s\n", serialNumber++, walkerNode->prnValue,
               walkerNode->fullName, describeRole(walkerNode->roleTag));
        walkerNode = walkerNode->nextLink;
    }
}

int enrollPresident(ClubMember **headSlot, long prnValue, const char *fullName)
{
    ClubMember *presidentNode;
    if (locateByPrn(*headSlot, prnValue) != NULL) {
        printf("PRN already registered.\n");
        return 0;
    }
    if (locateByRole(*headSlot, ROLE_PRESIDENT) != NULL) {
        printf("President already exists. Delete the current one first.\n");
        return 0;
    }
       presidentNode = buildMemberNode(prnValue, fullName, ROLE_PRESIDENT);
    if (presidentNode == NULL) return 0;
       presidentNode->nextLink = *headSlot;
    *headSlot = presidentNode;
    return 1;
}

int enrollSecretary(ClubMember **headSlot, long prnValue, const char *fullName)
{
    ClubMember *secretaryNode;
    ClubMember *tailNode;
      if (locateByPrn(*headSlot, prnValue) != NULL) {
        printf("PRN already registered.\n");
        return 0;
    }
    if (locateByRole(*headSlot, ROLE_SECRETARY) != NULL) {
        printf("Secretary already exists. Delete the current one first.\n");
        return 0;
    }
    secretaryNode = buildMemberNode(prnValue, fullName, ROLE_SECRETARY);
       if (secretaryNode == NULL) return 0;
    if (*headSlot == NULL) {
        *headSlot = secretaryNode;
    } else {
        tailNode = locateTail(*headSlot);
        tailNode->nextLink = secretaryNode;
    }
    return 1;
}

int enrollRegularMember(ClubMember **headSlot, long prnValue, const char *fullName)
{
    ClubMember *memberNode;
    ClubMember *trailNode = NULL;
       ClubMember *walkerNode = *headSlot;
    if (locateByPrn(*headSlot, prnValue) != NULL) {
        printf("PRN already registered.\n");
        return 0;
    }
       memberNode = buildMemberNode(prnValue, fullName, ROLE_MEMBER);
    if (memberNode == NULL) return 0;
    while (walkerNode != NULL && walkerNode->roleTag != ROLE_SECRETARY) {
        trailNode = walkerNode;
        walkerNode = walkerNode->nextLink;
    }
    memberNode->nextLink = walkerNode;
    if (trailNode == NULL)
          *headSlot = memberNode;
    else
        trailNode->nextLink = memberNode;
    return 1;
}

int removeByPrn(ClubMember **headSlot, long targetPrn)
{
      ClubMember *trailNode = NULL;
    ClubMember *walkerNode = *headSlot;
    while (walkerNode != NULL && walkerNode->prnValue != targetPrn) {
        trailNode = walkerNode;
        walkerNode = walkerNode->nextLink;
    }
    if (walkerNode == NULL) {
        printf("Student not found.\n");
        return 0;
    }
      if (trailNode == NULL)
        *headSlot = walkerNode->nextLink;
    else
        trailNode->nextLink = walkerNode->nextLink;
    free(walkerNode);
    return 1;
}

int removeByRole(ClubMember **headSlot, int targetRole)
{
    ClubMember *roleNode = locateByRole(*headSlot, targetRole);
     if (roleNode == NULL) {
        printf("%s not found.\n", describeRole(targetRole));
        return 0;
    }
    return removeByPrn(headSlot, roleNode->prnValue);
}

void wipeRegistry(ClubMember **headSlot)
{
     ClubMember *upcomingNode;
    while (*headSlot != NULL) {
        upcomingNode = (*headSlot)->nextLink;
        free(*headSlot);
        *headSlot = upcomingNode;
    }
}

void sortRegularsByPrn(ClubMember *listHead)
{
    int regularTotal = 0;
    int outerIdx;
       int innerIdx;
    int smallestIdx;
    ClubMember **regularSlots;
    ClubMember *walkerNode = listHead;
    long swapPrn;
    char swapName[NAME_CAPACITY];

      while (walkerNode != NULL) {
        if (walkerNode->roleTag == ROLE_MEMBER) regularTotal++;
        walkerNode = walkerNode->nextLink;
    }
    if (regularTotal < 2) return;

    regularSlots = (ClubMember **)malloc(sizeof(ClubMember *) * regularTotal);
    if (regularSlots == NULL) {
        printf("Memory allocation failed.\n");
        return;
    }
      outerIdx = 0;
    walkerNode = listHead;
    while (walkerNode != NULL) {
        if (walkerNode->roleTag == ROLE_MEMBER) regularSlots[outerIdx++] = walkerNode;
        walkerNode = walkerNode->nextLink;
    }
      for (outerIdx = 0; outerIdx < regularTotal - 1; outerIdx++) {
        smallestIdx = outerIdx;
        for (innerIdx = outerIdx + 1; innerIdx < regularTotal; innerIdx++) {
            if (regularSlots[innerIdx]->prnValue < regularSlots[smallestIdx]->prnValue)
                smallestIdx = innerIdx;
        }
        if (smallestIdx != outerIdx) {
              swapPrn = regularSlots[outerIdx]->prnValue;
            strcpy(swapName, regularSlots[outerIdx]->fullName);
            regularSlots[outerIdx]->prnValue = regularSlots[smallestIdx]->prnValue;
            strcpy(regularSlots[outerIdx]->fullName, regularSlots[smallestIdx]->fullName);
            regularSlots[smallestIdx]->prnValue = swapPrn;
            strcpy(regularSlots[smallestIdx]->fullName, swapName);
        }
      }
    free(regularSlots);
}

void flipListWithThreePointers(ClubMember **headSlot)
{
    ClubMember *behindPtr = NULL;
      ClubMember *currentPtr = *headSlot;
    ClubMember *aheadPtr = NULL;
    while (currentPtr != NULL) {
        aheadPtr = currentPtr->nextLink;
        currentPtr->nextLink = behindPtr;
        behindPtr = currentPtr;
        currentPtr = aheadPtr;
    }
      *headSlot = behindPtr;
}

ClubMember *detachNodeByRole(ClubMember **headSlot, int targetRole)
{
    ClubMember *trailNode = NULL;
    ClubMember *walkerNode = *headSlot;
    while (walkerNode != NULL && walkerNode->roleTag != targetRole) {
        trailNode = walkerNode;
          walkerNode = walkerNode->nextLink;
    }
    if (walkerNode == NULL) return NULL;
    if (trailNode == NULL)
        *headSlot = walkerNode->nextLink;
      else
        trailNode->nextLink = walkerNode->nextLink;
    walkerNode->nextLink = NULL;
    return walkerNode;
}

void demoteExtraRoles(ClubMember *listHead)
{
    int presidentSeen = 0;
    int secretarySeen = 0;
    ClubMember *walkerNode = listHead;
      while (walkerNode != NULL) {
        if (walkerNode->roleTag == ROLE_PRESIDENT) {
            if (presidentSeen) walkerNode->roleTag = ROLE_MEMBER;
            presidentSeen = 1;
        } else if (walkerNode->roleTag == ROLE_SECRETARY) {
             if (secretarySeen) walkerNode->roleTag = ROLE_MEMBER;
            secretarySeen = 1;
        }
        walkerNode = walkerNode->nextLink;
    }
}

void mergeRegistries(ClubMember **destinationSlot, ClubMember **sourceSlot)
{
    ClubMember *tailNode;
    ClubMember *presidentNode;
    ClubMember *secretaryNode;
    if (*sourceSlot == NULL) {
        printf("Second list is empty. Nothing to merge.\n");
        return;
    }
    if (*destinationSlot == NULL) {
        *destinationSlot = *sourceSlot;
    } else {
        tailNode = locateTail(*destinationSlot);
        tailNode->nextLink = *sourceSlot;
    }
    *sourceSlot = NULL;
    demoteExtraRoles(*destinationSlot);
    presidentNode = detachNodeByRole(destinationSlot, ROLE_PRESIDENT);
    secretaryNode = detachNodeByRole(destinationSlot, ROLE_SECRETARY);
    if (presidentNode != NULL) {
        presidentNode->nextLink = *destinationSlot;
        *destinationSlot = presidentNode;
    }
    if (secretaryNode != NULL) {
        if (*destinationSlot == NULL) {
            *destinationSlot = secretaryNode;
        } else {
            tailNode = locateTail(*destinationSlot);
            tailNode->nextLink = secretaryNode;
        }
    }
}

int readPrnFromUser(long *prnHolder)
{
    printf("Enter PRN: ");
    if (scanf("%ld", prnHolder) != 1) {
        while (getchar() != '\n');
        printf("Invalid PRN.\n");
        return 0;
    }
    return 1;
}

void readNameFromUser(char *nameHolder)
{
    printf("Enter Name: ");
    scanf(" %49[^\n]", nameHolder);
}

int chooseRegistrySlot(void)
{
    int slotPick = 0;
    printf("Select list (1 or 2): ");
    if (scanf("%d", &slotPick) != 1 || slotPick < 1 || slotPick > REGISTRY_COUNT) {
        while (getchar() != '\n');
        printf("Invalid list selection.\n");
        return -1;
    }
    return slotPick - 1;
}

void handleAddPresident(void)
{
    long prnEntry;
    char nameEntry[NAME_CAPACITY];
    int slotIdx = chooseRegistrySlot();
    if (slotIdx < 0 || !readPrnFromUser(&prnEntry)) return;
    readNameFromUser(nameEntry);
    if (enrollPresident(&clubRegistry[slotIdx], prnEntry, nameEntry))
        printf("President added.\n");
}

void handleAddSecretary(void)
{
    long prnEntry;
    char nameEntry[NAME_CAPACITY];
    int slotIdx = chooseRegistrySlot();
    if (slotIdx < 0 || !readPrnFromUser(&prnEntry)) return;
    readNameFromUser(nameEntry);
    if (enrollSecretary(&clubRegistry[slotIdx], prnEntry, nameEntry))
        printf("Secretary added.\n");
}

void handleAddMember(void)
{
    long prnEntry;
    char nameEntry[NAME_CAPACITY];
    int slotIdx = chooseRegistrySlot();
    if (slotIdx < 0 || !readPrnFromUser(&prnEntry)) return;
    readNameFromUser(nameEntry);
    if (enrollRegularMember(&clubRegistry[slotIdx], prnEntry, nameEntry))
        printf("Member added.\n");
}

void handleDeleteByPrn(void)
{
    long prnEntry;
    int slotIdx = chooseRegistrySlot();
    if (slotIdx < 0 || !readPrnFromUser(&prnEntry)) return;
    if (removeByPrn(&clubRegistry[slotIdx], prnEntry))
        printf("Member deleted.\n");
}

void handleDeletePresident(void)
{
    int slotIdx = chooseRegistrySlot();
    if (slotIdx < 0) return;
    if (removeByRole(&clubRegistry[slotIdx], ROLE_PRESIDENT))
        printf("President deleted.\n");
}

void handleDeleteSecretary(void)
{
    int slotIdx = chooseRegistrySlot();
    if (slotIdx < 0) return;
    if (removeByRole(&clubRegistry[slotIdx], ROLE_SECRETARY))
        printf("Secretary deleted.\n");
}

void handleCount(void)
{
    int slotIdx = chooseRegistrySlot();
    if (slotIdx < 0) return;
    printf("Total members: %d\n", tallyMembers(clubRegistry[slotIdx]));
}

void handleDisplay(void)
{
    int slotIdx = chooseRegistrySlot();
    if (slotIdx < 0) return;
    showRoster(clubRegistry[slotIdx]);
}

void handleSort(void)
{
    int slotIdx = chooseRegistrySlot();
    if (slotIdx < 0) return;
    if (clubRegistry[slotIdx] == NULL) {
        printf("No students registered.\n");
        return;
    }
    sortRegularsByPrn(clubRegistry[slotIdx]);
    printf("Regular members sorted by PRN (President and Secretary stay pinned).\n");
    showRoster(clubRegistry[slotIdx]);
}

void handleMerge(void)
{
    mergeRegistries(&clubRegistry[0], &clubRegistry[1]);
    printf("List 2 merged into List 1.\n");
    showRoster(clubRegistry[0]);
}

void handleReverse(void)
{
    int slotIdx = chooseRegistrySlot();
    if (slotIdx < 0) return;
    if (clubRegistry[slotIdx] == NULL) {
        printf("No students registered.\n");
        return;
    }
    flipListWithThreePointers(&clubRegistry[slotIdx]);
    printf("List reversed.\n");
    showRoster(clubRegistry[slotIdx]);
}

void handleDeleteAll(void)
{
    int slotIdx = chooseRegistrySlot();
    if (slotIdx < 0) return;
    wipeRegistry(&clubRegistry[slotIdx]);
    printf("All members deleted.\n");
    showRoster(clubRegistry[slotIdx]);
}

void printMenu(void)
{
    printf("\n===== PINNACLE CLUB MANAGER =====\n");
    printf(" 1. Add President\n");
    printf(" 2. Add Secretary\n");
    printf(" 3. Add Member\n");
    printf(" 4. Delete Member by PRN\n");
    printf(" 5. Delete President\n");
    printf(" 6. Delete Secretary\n");
    printf(" 7. Count Members\n");
    printf(" 8. Display Members\n");
    printf(" 9. Sort by PRN\n");
    printf("10. Merge List 2 into List 1\n");
    printf("11. Reverse List\n");
    printf("12. Delete All Members\n");
    printf(" 0. Exit\n");
    printf("Choice: ");
}

int main(void)
{
    int menuChoice = -1;
    int slotCounter;

    while (menuChoice != 0) {
        printMenu();
        if (scanf("%d", &menuChoice) != 1) {
            while (getchar() != '\n');
            printf("Invalid input.\n");
            menuChoice = -1;
            continue;
        }
        switch (menuChoice) {
            case 1:  handleAddPresident();   break;
            case 2:  handleAddSecretary();   break;
            case 3:  handleAddMember();      break;
            case 4:  handleDeleteByPrn();    break;
            case 5:  handleDeletePresident(); break;
            case 6:  handleDeleteSecretary(); break;
            case 7:  handleCount();          break;
            case 8:  handleDisplay();        break;
            case 9:  handleSort();           break;
            case 10: handleMerge();          break;
            case 11: handleReverse();        break;
            case 12: handleDeleteAll();      break;
            case 0:  printf("Exiting.\n");   break;
            default: printf("Invalid choice.\n");
        }
    }

    for (slotCounter = 0; slotCounter < REGISTRY_COUNT; slotCounter++)
        wipeRegistry(&clubRegistry[slotCounter]);
    return 0;
}