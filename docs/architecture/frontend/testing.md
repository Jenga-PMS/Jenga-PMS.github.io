# Frontend Testing Report

## Goal
This report summarizes how frontend tests are implemented in `frontend/jenga` and which tools are used.
The objective is to verify user-relevant behavior of the UI in a repeatable way.

## Scope
The test files are located in:

`frontend/jenga/src/components/*.test.tsx`

The current test focus is:
- rendering and UI state changes
- user interactions (for example click, drop, and dialog toggles)
- expected calls to provider and API-related functions

The current quality focus is on unit and component-near tests.
End-to-end tests across full user journeys are still pending.

## Test Setup and Execution
Tests are executed from `frontend/jenga`.

Run all tests:

```bash
npm test -- --run
```

Run tests in watch mode during development:

```bash
npm test
```

Run one file:

```bash
npm test -- --run src/components/Auth.test.tsx
```

## Used Libraries
The following libraries are used in the frontend test setup:

- `vitest`: test runner and assertion framework
- `@solidjs/testing-library`: SolidJS render helpers and DOM queries
- `@testing-library/user-event`: realistic user interactions
- `@testing-library/jest-dom`: additional DOM matchers (for example `toBeInTheDocument`)
- `jsdom`: browser-like environment for test execution

## Example Test Case
The following example shows a small interaction test from the frontend context:

```tsx
test("calls logout in logged-in state", async () => {
  const user = userEvent.setup();
  const logout = vi.fn();

  render(() => <Auth />, {
    wrapper: createTestWrapper({
      auth: {
        isLoggedIn: () => true,
        jwt: () => ({ username: "alice" }),
        logout,
      },
    }),
  });

  await user.click(screen.getByRole("button", { name: "logout" }));
  expect(logout).toHaveBeenCalledTimes(1);
});
```

This test verifies one concrete behavior: when the logout button is clicked, the provider logout function is called.

## Open Test Areas
End-to-end test coverage is not implemented yet.
This can be added with tools such as Selenium, Playwright, or Cypress.

## Reference
Official SolidJS testing guide:

- https://docs.solidjs.com/guides/testing
