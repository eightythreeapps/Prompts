# Full Stack Web app - Claude Code Rules

## Project Overview
Full stack web application using a SQLite Database - add project specific context here.

- **Backend**: Node.js/Express API with TypeScript, Drizzle ORM, SQLite
- **Frontend**: React 18 with TypeScript, Vite, TailwindCSS, shadcn/ui
- **Desktop**: Electron application
- **Architecture**: Nx monorepo with modular library structure
- **Database**: SQLite with Drizzle ORM migrations
- **Authentication**: JWT-based with PIN codes and role-based access

## Architecture & Code Organization

### Nx Monorepo Structure

- `apps/api/` - Express.js backend API server
- `apps/web/` - React frontend client
- `apps/desktop/` - Electron desktop application
- `libs/` - Shared libraries (ui, data-access, shared-types, config)
- `testing/` - Centralized testing utilities and mocks

### Path Mapping Standards

- Use `@/` for internal app imports (web app only)
- Use `@kanora/package-name` for shared libraries
- All path mappings are defined in `tsconfig.base.json`
- ESLint enforces module boundaries with `@nx/enforce-module-boundaries`

```typescript
// Correct imports
import { Button } from '@/components/ui/button'; // Web app internal
import { ApiService } from '@kanora/data-access'; // Shared library
import { MediaType } from '@kanora/shared-types'; // Shared types
```

## Backend API Patterns

### Controller Structure

Follow the established controller pattern:

```typescript
// Standard controller pattern
export const controllerName = async (req: Request, res: Response) => {
  try {
    // Extract query parameters with defaults
    const page = parseInt(req.query.page as string) || 1;
    const pageSize = parseInt(req.query.pageSize as string) || 20;

    // Call service layer
    const result = await Service.method(params);

    // Return standardized API response
    const response: ApiResponse<typeof result> = {
      success: true,
      data: result,
      timestamp: new Date().toISOString(),
    };
    res.json(response);
  } catch (error) {
    console.error('Error in controllerName:', error);
    const response: ApiResponse<null> = {
      success: false,
      error: error instanceof Error ? error.message : 'Unknown error',
      timestamp: new Date().toISOString(),
    };
    res.status(500).json(response);
  }
};
```

### Database Schema Organization

- All schemas in `apps/api/src/db/schema/` with modular files
- Re-exported through `schema/index.ts`
- Use Drizzle ORM patterns consistently
- Migrations in `apps/api/src/db/migrations/`

### Service Layer Pattern

- Separate business logic into service classes
- Services handle database operations and business rules
- Controllers are thin and delegate to services
- Example: `ArtistService`, `LibraryService`, etc.

## Frontend Patterns

### Component Structure

- Pages in `apps/web/src/app/pages/`
- Shared components in `apps/web/src/app/components/`
- UI components in `apps/web/src/components/ui/` (shadcn/ui)
- Follow React functional component patterns

### State Management

- React Context for global state (AuthContext etc)
- React hooks for local component state
- Custom hooks in `apps/web/src/app/hooks/`

### Styling Standards

- TailwindCSS for styling with design system classes
- shadcn/ui components for consistent UI patterns
- CSS modules for component-specific styles when needed
- Dark/light theme support with `next-themes`

## Testing Standards

### Test Organization

- Unit tests alongside source files with `.spec.ts` suffix
- Integration tests in dedicated test directories
- Centralized mocking utilities in `testing/src/lib/mocks/`
- MSW for HTTP mocking

### Coverage Requirements (Realistic & Incremental)

```typescript
// jest.config.ts - Start with achievable targets
coverageThreshold: {
  global: {
    lines: 10,      // Baseline - gradually increase
    statements: 10,
    functions: 20,  // Focus on testing functions first
    branches: 30,   // Branch coverage grows over time
  },
}
```

### Controller Testing Pattern

```typescript
// Standard controller test pattern
describe('Controller Name', () => {
  let mockRequest: Partial<Request>;
  let mockResponse: Partial<Response>;
  let jsonSpy: jest.Mock;
  let statusSpy: jest.Mock;

  beforeEach(() => {
    jsonSpy = jest.fn();
    statusSpy = jest.fn().mockReturnThis();
    mockRequest = { query: {} };
    mockResponse = { json: jsonSpy, status: statusSpy };
    jest.clearAllMocks();
  });

  // Test happy path and error conditions
});
```

### Mock Factories

Use centralized mock factories from `testing/src/lib/mocks/createDbMock.ts`:

```typescript
const { dbMock, chains } = createDbMock();
jest.mock('../../db/config', () => ({ db: dbMock }));
```

## CI/CD & Build Standards

### Nx Command Patterns

Use `nx run-many` for multi-project operations:

```json
{
  "test": "nx run-many --target=test --all",
  "lint": "nx run-many --target=lint --all",
  "build": "nx run-many --target=build --all"
}
```

### Dependency Management

- All dependencies hoisted to root `package.json`
- No dependencies in individual app `package.json` files
- Use exact versions for critical dependencies

### Pre-commit Requirements

- All tests must pass: `npm run test`
- Linting must pass: `npm run lint`
- Build must succeed: `npm run build`
- Format code: `npm run format`

## Security & Performance

### API Security

- Rate limiting on all API endpoints
- JWT authentication with proper token management
- CORS configured for frontend domains only
- Request size limiting and validation
- Security headers via Helmet.js

### Database Security

- Parameterized queries via Drizzle ORM
- Input validation with Zod schemas
- No raw SQL queries without sanitization

## Error Handling Standards

### API Error Responses

```typescript
// Standardized error response
const response: ApiResponse<null> = {
  success: false,
  error: error instanceof Error ? error.message : 'Unknown error',
  timestamp: new Date().toISOString(),
};
```

### Frontend Error Handling

- Error boundaries for React components
- Toast notifications for user feedback
- Graceful degradation for network issues

## Development Workflow

### Branch Strategy

- `main` branch for stable releases
- Feature branches from `main`
- Pull requests require passing CI

### Local Development

```bash
# Start API server
npm run dev

# Start web client
npm run dev:client

# Run all tests
npm run test

# Database operations
npm run db:generate  # Generate migrations
npm run db:migrate   # Run migrations
npm run db:studio    # Open Drizzle Studio
```

## Code Quality Rules

### TypeScript Standards

- Strict TypeScript configuration
- Proper typing for all function parameters and returns
- Use discriminated unions for complex state
- Avoid `any` type unless absolutely necessary

### Import Organization

```typescript
// 1. Node modules
import express from 'express';
import path from 'path';

// 2. Internal library imports
import { MediaType } from '@kanora/shared-types';
import { ApiService } from '@kanora/data-access';

// 3. Relative imports
import { Button } from '@/components/ui/button';
import './styles.css';
```

### Naming Conventions

- `camelCase` for variables and functions
- `PascalCase` for components and types
- `SCREAMING_SNAKE_CASE` for constants
- `kebab-case` for file names
- Descriptive names over abbreviations

## Performance Guidelines

### Bundle Size

- Use dynamic imports for code splitting
- Avoid importing entire libraries when possible
- Monitor bundle size with Nx build analysis

### Database Queries

- Use proper indexing in SQLite schema
- Implement pagination for large datasets
- Use select specific fields rather than `SELECT *`

### Frontend Performance

- Lazy load routes and components
- Optimize images with Sharp processing
- Use React.memo() for expensive components

## Debugging & Monitoring

### Logging Standards

- Use structured logging with timestamps
- Different log levels for development vs production
- No sensitive data in logs (tokens, passwords)

### Error Tracking

- Console.error for backend errors with context
- Toast notifications for user-facing errors
- Health check endpoint at `/health`

---

## Analysis of Current Rule Adherence

Based on my review, the codebase generally follows good practices but has some areas for improvement:

### ✅ Well-Followed Patterns

- Consistent API response structure with `ApiResponse<T>`
- Proper Nx monorepo organization
- Good separation between controllers, services, and routes
- TypeScript path mappings correctly configured
- ESLint module boundaries properly set up

### ⚠️ Areas Needing Attention

- **Test Coverage**: Current coverage thresholds may be unrealistic (existing rules suggest reducing from 85% to achievable levels)
- **Dependency Hoisting**: Some dependencies may still exist in individual app package.json files
- **Error Handling**: Some controllers may need more robust error handling patterns
- **Import Organization**: Mixed import ordering in some files

### 🔧 Recommended Improvements

1. **Standardize Error Handling**: Ensure all controllers follow the same error response pattern
2. **Test Coverage**: Implement incremental coverage targets as outlined in existing rules
3. **Import Sorting**: Add import sorting rules to ESLint configuration
4. **Component Testing**: Increase React component test coverage using React Testing Library

# Development Session Management

## Mandatory Session Workflow

### 🚀 Session Start Protocol

**CRITICAL: NO DEVELOPMENT WORK BEGINS WITHOUT A SESSION**

1. **Session Creation (Required First Step)**

   ```bash
   npm run docs:wiki:session "Session Topic" "Clear Objective Description"
   ```

   - Use descriptive, specific topics (e.g., "CD Import UI Improvements", "Authentication Refactor")
   - Objectives should be actionable and measurable
   - **DO NOT START ANY CODING UNTIL SESSION IS CREATED**

2. **Session Context Setup**
   - If working from a GitHub issue/ticket, include the URL in the session document
   - Document any background from previous sessions
   - Clearly state the problem being solved

### 📋 During Development

**Use TodoWrite tool extensively to track progress:**

```typescript
// Example TodoWrite usage patterns
TodoWrite([
  { id: '1', content: 'Investigate root cause of navigation issue', status: 'in_progress' },
  { id: '2', content: 'Fix icon import error', status: 'pending' },
  { id: '3', content: 'Update UI components for consistency', status: 'pending' },
  { id: '4', content: 'Test navigation flow end-to-end', status: 'pending' },
]);
```

**Required Documentation During Session:**

- Mark todos as completed IMMEDIATELY when finished
- Document technical decisions and their rationale
- Record any architecture changes or new patterns discovered
- Note files modified and their purposes

### 📝 Session End Protocol

**CRITICAL: EVERY SESSION MUST BE DOCUMENTED AND COMMITTED**

1. **Update Session Document**
   - Complete all sections with actual accomplishments
   - Document technical decisions made
   - List all files changed with clear descriptions
   - Record learnings and insights gained
   - Define clear next steps for future work

2. **Quality Gates Before Commit**

   ```bash
   npm run build    # Must pass
   npm run lint     # Must pass (warnings acceptable)
   npm run test     # Must pass
   ```

3. **Git Commit to Wiki (MANDATORY)**

   ```bash
   git add Development-Session-YYYY-MM-DD-[Topic].md
   git commit -m "Add development session: [Topic] (YYYY-MM-DD)

   - [Key accomplishment 1]
   - [Key accomplishment 2]
   - [Key accomplishment 3]

   🤖 Generated with [Claude Code](https://claude.ai/code)

   Co-Authored-By: Claude <noreply@anthropic.com>"

   git push origin master
   ```

## Session Types and Templates

### 🐛 Bug Fix Sessions

- **Topic Format**: "Fix [Component] [Issue Description]"
- **Focus**: Root cause analysis, minimal change approach
- **Documentation**: Emphasize the bug reproduction and solution verification

### ✨ Feature Development Sessions

- **Topic Format**: "[Feature Name] Implementation"
- **Focus**: Architecture decisions, component design patterns
- **Documentation**: Design rationale, integration points, future extensibility

### 🔧 Refactoring Sessions

- **Topic Format**: "[Component/Area] Refactoring"
- **Focus**: Code quality improvements, performance optimizations
- **Documentation**: Before/after comparisons, impact analysis

### 📚 Research/Investigation Sessions

- **Topic Format**: "[Technology/Problem] Investigation"
- **Focus**: Understanding existing systems, evaluating solutions
- **Documentation**: Findings, recommendations, implementation paths

## Enforcement Rules

### ❌ Prohibited Actions

- **NEVER** start coding without creating a session document first
- **NEVER** end a session without completing and committing documentation
- **NEVER** leave TodoWrite tasks in "in_progress" state at session end
- **NEVER** commit code changes without updating the session document

### ✅ Required Actions

- **ALWAYS** use the TodoWrite tool to track multi-step tasks
- **ALWAYS** document the "why" behind technical decisions
- **ALWAYS** run quality gates before session completion
- **ALWAYS** commit session documentation to the wiki
- **ALWAYS** define clear next steps for future sessions

### 🔄 Session Handoffs

When continuing work from a previous session:

- Reference the previous session document
- Summarize what was accomplished previously
- Clearly state what remains to be done
- Link related GitHub issues/tickets

## Documentation Quality Standards

### Required Sections (All Must Be Completed)

- **Tasks Accomplished**: Specific, measurable outcomes
- **Technical Decisions**: Architecture choices with rationale
- **Files Changed**: Every modified file with purpose explanation
- **Testing**: What was tested and how
- **Knowledge & Learnings**: Insights gained about codebase/domain
- **Next Steps**: Actionable items for future sessions

### Writing Style

- Use active voice and specific technical language
- Include code snippets for complex changes
- Reference line numbers when discussing specific modifications
- Document both successful approaches and dead ends explored

## Integration with Project Workflow

### GitHub Issue Integration

- Always reference related issues in session documents
- Update issue status based on session outcomes
- Link session document URLs in issue comments for traceability

### Code Review Preparation

- Session documents serve as context for code reviews
- Include rationale for controversial or complex changes
- Document any deviations from established patterns

### Knowledge Management

- Sessions create searchable history of project evolution
- Enable efficient onboarding of new developers
- Provide context for future debugging and maintenance

---

**Remember: Development session management is not optional - it's a core part of the development workflow that ensures quality, traceability, and knowledge preservation for the Kanora project.**
