# Balancer - React Native App Rules

## Project Overview
React Native app for tracking shared expenses between friends with smart debt minimization algorithm.

## Business Logic & Purpose
**Main Goal**: Minimize money transfers between friends while tracking shared expenses.

### Core Concepts
**Groups** - Friends unite in groups to track shared expenses
- Each group has a unified currency for calculations
- Group members participate in shared expenses
- Automatically calculated debts between all members

**Transactions** - Records of shared expenses
- Who paid (`payerId`) - the person who made the payment
- Original amount and currency (`amountOriginal`, `currencyOriginal`)
- Fixed exchange rate to group currency (`exchangeRateToGroup`) 
- Converted amount (`amountInGroupCurrency`)
- How expense is split between participants (`split` with `ratio` coefficients)

**Smart Expense Splitting** - Flexible distribution system
- Each participant can have custom coefficient (ratio): 1, 2, 0.5, etc.
- Participants can be excluded from specific expenses
- Amount is divided proportionally to coefficients

**Automatic Debt Calculation** - Transfer minimization algorithm
- After each transaction, all debts are recalculated
- Debts are netted (if A owes B and B owes A, only difference remains)
- Shows final picture: who owes whom and how much
- Goal: minimum number of transfers to "settle" all debts

**Multi-currency Support**
- Group operates in unified currency
- Transactions can be in different currencies  
- Exchange rate is fixed at transaction creation time
- All calculations within group use unified currency

### Key User Flows
1. **Create Group** → Add friends → Set group currency
2. **Add Transaction** → Who paid → Set amount/currency → Split between participants with ratios
3. **View Debts** → See who owes whom → Understand "who pays next"
4. **Settle Debts** → Make transfers → Mark as settled

### Debt Calculation Algorithm (`calculateDebts`)
- Uses Decimal.js for precise financial calculations
- Maintains debt map between all participants
- Adding transaction: all participants except payer owe their share to payer
- Deleting transaction: rolls back debt changes
- Nets debts (if A owes B 100, B owes A 60, result: A owes B 40)
- Supports debt reversals when debt changes direction

## Tech Stack
- **Framework**: React Native
- **Language**: TypeScript
- **Backend**: Firebase (Auth, Firestore, Functions, Storage)
- **UI Library**: Tamagui
- **State Management**: Tanstack React Query
- **Navigation**: React Navigation
- **Testing**: Jest (unit tests) + Detox (e2e tests)
- **Code Quality**: ESLint + Prettier

## Code Style & Patterns

### File Structure
- `/app/components/` - Reusable UI components
- `/app/screens/` - Screen components
- `/app/contexts/` - React contexts
- `/app/hooks/queries/` - React Query hooks
- `/app/navigations/` - Navigation components and themes
- `/app/repositories/` - Data layer abstractions
- `/app/utils/` - Pure utility functions
- `/types/` - Shared TypeScript types
- `/functions/` - Firebase Cloud Functions
- `/docs/` - Project documentation
- `/e2e/` - End-to-end tests (Detox)

### Import Conventions
- Use `@shared` alias for types: `import {User} from '@shared/user'`
- Group imports: React → Libraries → Local
- Use absolute imports for app folder structure

### Component Guidelines
- Use arrow functions for components: `export default () => {}`
- Prefer functional components with hooks
- Use Tamagui components over React Native primitives
- Implement proper TypeScript interfaces for props

### State Management
- Use React Query for server state
- Use React Context for app-wide client state
- Configure queries with appropriate staleTime and gcTime

### Firebase Patterns
- Use repositories for Firebase operations
- Implement proper error handling
- Use TypeScript interfaces for Firestore documents
- Follow Firebase security rules best practices

### Performance
- Use React.memo for expensive components
- Implement proper key props for lists
- Use Reanimated for complex animations
- Optimize images with react-native-fast-image

### Testing
- Write unit tests for utilities and services
- Use Jest for unit testing framework
- Use Detox for end-to-end testing
- Mock Firebase services in unit tests
- Write e2e tests for critical user flows (auth, group management, transactions)

### Key Components & Firebase Architecture
**Core Screens**:
- `groups.tsx` - List of user's groups with balance preview
- `transactions.tsx` - Transaction history within a group
- `debts.tsx` - Debt summary showing who owes whom (main screen for "who pays next")

**Key Components**:
- `CreateTransactionModal` - Complex form for adding expenses with multi-currency and split logic
- `TransactionCard` - Display transaction with payer, amount, split info
- `GroupCard` - Group overview with member avatars and balance
- `SwipeableGroupCard` - Group card with swipe actions (edit/delete)

**Firebase Cloud Functions** (`/functions/src/`):
- `createTransaction.ts` - Handles transaction creation and debt recalculation
- `deleteTransaction.ts` - Handles transaction deletion and debt rollback
- `deleteGroup.ts` - Handles group deletion and cleanup
- `getGroupPreview.ts` - Returns group preview data for invites
- `joinGroupById.ts` - Handles joining group by invite ID
- `leaveGroupById.ts` - Handles leaving group and debt recalculation
- `updateExchangeRates.ts` - Updates currency exchange rates

**Firebase Utils** (`/functions/src/utils/`):
- `transactionUtils.ts` - Core debt calculation algorithm
- `balanceUtils.ts` - Balance calculation utilities

**Navigation Architecture**:
- `AppNavigator.tsx` - Main app navigation (auth → main app)
- `TabNavigator.tsx` - Bottom tab navigation (groups, profile)
- `GroupTabNavigator.tsx` - Tab navigation within groups (transactions, debts)
- `navigationTheme.ts` - Custom navigation theme configuration

**Documentation** (`/docs/`):
- `ui-ux-styleguide.md` - UI/UX design guidelines
- `anonymous-google-auth.md` - Anonymous authentication flow
- `group-invitations-and-navigation-improvements.md` - Group invitation system

**End-to-End Tests** (`/e2e/`):
- Detox-based e2e tests for critical user flows
- Test authentication, group creation, transaction management
- CI/CD integration with test artifacts in `/artifacts/`

**Data Flow**:
1. User creates transaction → Cloud Function calculates debts → Updates group
2. All calculations in Cloud Functions for consistency
3. Fetch updates from Firestore
4. React Query for caching and optimistic updates

## Code Generation Rules
1. Always use TypeScript with proper types
2. Follow existing naming conventions
3. Use Tamagui styled components
4. Implement React Query patterns for API calls
5. Add proper error boundaries and loading states
6. Use established project structure
7. Follow ESLint and Prettier configurations
8. Include proper imports and dependencies
9. Follow existing navigation architecture patterns
10. Write both unit tests (Jest) and e2e tests (Detox) for new features
11. Document complex business logic in `/docs/` folder
12. Understand financial calculations require precision (use Decimal.js)
13. All debt calculations happen in Firebase Cloud Functions
14. Multi-currency support with fixed exchange rates
15. Implement proper split logic with ratios and participant selection 