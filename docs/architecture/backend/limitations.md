# Limitations
Currently, the Jenga API offers some limitations:

- There is no permission on a project basis. All projects are visible to all logged-in users. This limits the usage of Jenga significantly
- Jenga does not use the file system to store various files, e.g. attachments for tickets or user avatars for profiles
- It offers no configuration for users
- The access token can't be refreshed -> After the token expires, another login with credentials is required
- Jenga as a whole offers no configurations, other than these needed for running the app
- No panel for admins to help with maintance related activities (Manage accounts/projects, dashboard)
