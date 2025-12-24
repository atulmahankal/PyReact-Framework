## Testing Guidelines:

- Generate frontend & backend test cases where requested; prefer unit and integration tests.
- Use SQLite for tests where practical and delete test DB after runs.
- Keep tests lightweight to reduce CI memory usage.
- Backend: pytest, prefer async tests when appropriate.
- Use SQLite for tests where feasible; delete test DB after run to conserve disk space.
- Frontend: basic component + API mocking tests only.
- Avoid heavy end-to-end suites unless explicitly requested.