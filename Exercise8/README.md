# Exercise 8: Creating a "Hello World" Jenkins Job

This exercise publishes a small shell script to GitHub, configures a Jenkins Freestyle project to retrieve it, and runs it as a build step. A successful build prints `Hello, Jenkins!` in the Jenkins Console Output.

## Prerequisites

- A GitHub account.
- Git installed locally.
- Jenkins running and accessible at [http://localhost:8080/](http://localhost:8080/).
- The Jenkins Git plugin installed.

## 1. Create a GitHub repository

1. Sign in to [GitHub](https://github.com/).
2. Select **+** → **New repository**.
3. Set the repository name to `devops-sample-code`.
4. Optionally add the description `A demo repository for Jenkins scripting.`
5. Select **Public**, then select **Create repository**.

The repository used in the screenshots is [AyushJha004/devops-sample-code](https://github.com/AyushJha004/devops-sample-code). Substitute your own repository URL if you created it under a different account.

## 2. Create a fine-grained personal access token

GitHub requires a personal access token instead of an account password when authenticating Git operations over HTTPS.

1. Open [GitHub fine-grained personal access tokens](https://github.com/settings/personal-access-tokens/new) or read [GitHub's token management guide](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens).
2. Enter a descriptive token name and choose an expiration.
3. Under **Repository access**, select **Only select repositories** and choose `devops-sample-code`.
4. Under **Repository permissions**, grant **Contents: Read and write** to push commits. Do not grant permissions the workflow does not need.
5. Generate the token and copy it immediately to a secure password manager; GitHub will not show it again.

> **Security:** Never add the token to this repository, paste it into a README, or share it in a screenshot. For this public repository, Jenkins can clone it without credentials. The token is for your authenticated Git operations, such as pushing changes.

## 3. Create and push `hello-world.sh`

Create a file named `hello-world.sh` in the repository with this content:

```bash
#!/bin/bash
echo "Hello, Jenkins!"
```

Then commit and push it from a terminal opened in the local repository:

```bash
git add hello-world.sh
git commit -m "Add hello world shell script"
git push -u origin main
```

When Git prompts for HTTPS credentials, enter your GitHub username and use the fine-grained token as the password. If your repository's default branch is not `main`, replace `main` with its branch name.

## 4. Create a Jenkins Freestyle project

1. Open [Jenkins](http://localhost:8080/).
2. Select **New Item**, enter `Hello-World-Job`, select **Freestyle project**, then select **OK**.
3. Under **Source Code Management**, select **Git** and enter the repository URL:

   ```text
   https://github.com/AyushJha004/devops-sample-code.git
   ```

4. For a repository whose default branch is `main`, set **Branch Specifier** to `*/main`. If the repository uses a different default branch, use `*/<branch-name>`.
5. Under **Build Steps**, select **Add build step** → **Execute shell**, then enter:

   ```sh
   sh hello-world.sh
   ```

6. Select **Save**.

## 5. Run and verify the job

Open `Hello-World-Job` and select **Build Now**. Open the new build and select **Console Output**. The output should include:

```text
Hello, Jenkins!
Finished: SUCCESS
```

## Screenshots

### Creating the shell script and pushing it to GitHub

![Create and push hello-world.sh](./Screenshot%202026-10-10%20135612.png)

### Opening fine-grained token settings

![Fine-grained personal access token settings](./Screenshot%202026-10-10%20140234.png)

![Token creation page](./Screenshot%202026-10-10%20141123.png)

![Token repository access and permissions](./Screenshot%202026-10-10%20143900.png)

### Creating and configuring the Jenkins project

![Create the Hello-World-Job Freestyle project](./Screenshot%202026-10-10%20143723.png)

![Jenkins project general settings](./Screenshot%202026-10-10%20143114.png)

![Configure Git repository and branch](./Screenshot%202026-10-10%20142457.png)

![Configure the shell build step](./Screenshot%202026-10-10%20143413.png)

### Successful Jenkins build

![Hello World Jenkins build console output](./Screenshot%202026-10-10%20143258.png)
