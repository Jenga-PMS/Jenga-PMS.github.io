# Jenga Backend

Jenga backend using Quarkus, the Supersonic Subatomic Java Framework.

-----

## Configuration and Setup

Before running the application, you must configure security keys and API credentials.

### 1\. JWT Keypair Setup

For JWT authentication, you must generate a keypair.

> **Note:** These files are not needed in `dev` mode but are required for production builds.

Generate the keypair using `openssl`:

```
# Generate the private key
openssl genrsa -out privateKey.pem 2048

# Generate the public key from the private key
openssl rsa -in privateKey.pem -pubout -out publicKey.pem
```

Place the generated files in the `src/main/resources/` directory:

  * `src/main/resources/privateKey.pem`
  * `src/main/resources/publicKey.pem`

### 2\. Environment Configuration

The application requires API keys for AI and search functionality. These are managed in two files:

  * **`.env`:** (At the project root) Used to store your secret keys.
  * **`application.properties`:** (In `src/main/resources`) Used to configure the application and read the keys from the environment.

#### Gemini (Langchain4j)

**`.env`**

```
QUARKUS_LANGCHAIN4J_AI_GEMINI_API_KEY=your_gemini_api_key_goes_here
```

**`application.properties`**

```
# Gemini AI Model Configuration
quarkus.langchain4j.ai.gemini.api-key=${QUARKUS_LANGCHAIN4J_AI_GEMINI_API_KEY:}
quarkus.langchain4j.ai.gemini.chat-model.model-id=gemini-1.5-flash
```

#### Google Web Search Tool

This enables the AI's `WebSearchTool` via the Google Custom Search Engine (CSE) API.

**Get Credentials:**

  * **API Key:** Get from [Google Cloud Console](https://console.cloud.google.com/apis/credentials). Enable the **"Custom Search API"**.
  * **Search Engine ID:** Get from the [Programmable Search Engine panel](https://www.google.com/search?q=https.programmablesearchengine.google.com/controlpanel/all). Create a new engine, select **"Search the entire web"**, and copy the "Search engine ID" from the Setup tab.

**`.env`**

```
# Google Custom Search Credentials
GOOGLE_API_KEY=your_google_api_key_goes_here
GOOGLE_CSE_ID=your_google_cse_id_goes_here
```

**`application.properties`**

```
# Google Web Search Configuration
quarkus.rest-client.google-search-api.url=httpss://www.googleapis.com
google.api.key=${GOOGLE_API_KEY}
google.cse.id=${GOOGLE_CSE_ID}
```

-----

## Running and Building

### Development Mode

Run your application in dev mode, which enables live coding:

```
./mvnw quarkus:dev
```

  * **Dev UI:** [http://localhost:8080/q/dev/](https://www.google.com/search?q=http://localhost:8080/q/dev/)
  * **Swagger:** [http://localhost:8080/swagger](https://www.google.com/search?q=http://localhost:8080/swagger)

### Packaging the Application

Package the application using:

```
./mvnw package
```

This produces a `quarkus-run.jar` file in `target/quarkus-app/`.

Run the application using:

```
java -jar target/quarkus-app/quarkus-run.jar
```

> **To build an *über-jar*** (a single executable file), run:
>
> ```
> ./mvnw package -Dquarkus.package.jar.type=uber-jar
> ```
>
> You can then run it using: `java -jar target/*-runner.jar`

### Creating a Native Executable

You can create a native executable using:

```
./mvnw package -Dnative
```

If you don't have GraalVM installed, run the build in a container:

```
./mvnw package -Dnative -Dquarkus.native.container-build=true
```

You can then execute your native executable with: `./target/jenga-0.0.1-SNAPSHOT-runner`

-----

### **`docs/export-issues.md`**

-----

# How to Export GitHub Issues

This guide covers the steps to export all issues from a GitHub repository to a JSON file using the official GitHub CLI.

*See also: [Ticket \#56](https://www.google.com/search?q=httpss://github.com/Jenga-PMS/Jenga/issues/56)*

-----

### Step 1: Install the GitHub CLI

If you are on a Mac, you can use Homebrew. For other systems, see the [official installation guide](https://www.google.com/search?q=httpss://github.com/cli/cli%23installation).

```
brew install gh
```

### Step 2: Log In

You must authenticate with your GitHub account. This command specifically requests the `read:project` scope needed to access project information.

```
gh auth login -h github.com -s read:project
```

### Step 3: Export All Issues

Run the command below to fetch all issues (both open and closed) from the `Jenga-PMS/Jenga` repository. The data will be saved to a file named `all-issues.json`.

```
gh issue list \
  --repo Jenga-PMS/Jenga \
  --state all \
  --json number,title,body,state,assignees,labels,milestone,comments,createdAt,updatedAt,projectItems \
  > all-issues.json
```

> **Note:** Custom fields like "Size," "Estimate," and "Priority" (which may exist in your project's board) cannot be extracted using this basic `gh issue list` command.