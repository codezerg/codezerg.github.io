# NuGet with GitHub Packages: A Complete Guide

GitHub Packages provides a powerful, integrated package hosting solution, allowing you to publish and consume NuGet packages directly from your GitHub repositories. This guide will walk you through the process of authenticating, publishing, and consuming NuGet packages using GitHub Packages.

---

## 1. **Create a Personal Access Token (PAT)**

To interact with GitHub Packages, you need to authenticate using a Personal Access Token (PAT).

### Steps:

1. **Generate a PAT:**
   - Go to [GitHub Settings → Developer settings → Personal access tokens](https://github.com/settings/tokens).
   - Click **"Generate new token"** (choose classic or fine-grained as needed).
   - **Select the following scopes:**
     - `read:packages` (to install packages)
     - `write:packages` (to publish packages)
     - `repo` (if your repository is private)
   - Generate and **copy the token**. Save it securely, as you won’t be able to view it again.

---

## 2. **Configure NuGet to Use Your Token**

You need to tell NuGet how to authenticate to GitHub Packages using your PAT.

### **A. Using NuGet CLI or Visual Studio**

1. **Edit your `NuGet.config`** (either at the project or user level):

   ```xml
   <configuration>
     <packageSources>
       <add key="github" value="https://nuget.pkg.github.com/OWNER/index.json" />
     </packageSources>
     <packageSourceCredentials>
       <github>
         <add key="Username" value="GITHUB_USERNAME" />
         <add key="ClearTextPassword" value="YOUR_TOKEN" />
       </github>
     </packageSourceCredentials>
   </configuration>
   ```
   - Replace `OWNER` with your GitHub username or organization.
   - Replace `GITHUB_USERNAME` with your GitHub username.
   - Replace `YOUR_TOKEN` with the PAT you generated.

2. **Alternatively, add the source via CLI:**

   ```sh
   dotnet nuget add source --username GITHUB_USERNAME --password YOUR_TOKEN --store-password-in-clear-text --name github "https://nuget.pkg.github.com/OWNER/index.json"
   ```

   - Again, replace `OWNER`, `GITHUB_USERNAME`, and `YOUR_TOKEN` appropriately.

---

## 3. **Publish a NuGet Package to GitHub Packages**

Once your authentication is set up, you can publish packages.

### Using the .NET CLI:

```sh
dotnet nuget push MyPackage.nupkg --source github
```

### Using the NuGet CLI:

```sh
nuget push MyPackage.nupkg -Source github
```

- `MyPackage.nupkg` should be replaced with the path to your NuGet package file.

---

## 4. **Consume a NuGet Package from GitHub Packages**

To use packages hosted on GitHub Packages:

1. **Ensure the GitHub source is added** to your `NuGet.config` (as shown above).
2. **Install the package as usual:**

   ```sh
   dotnet add package MyPackage
   ```

   - Replace `MyPackage` with the actual package name.

---

## **Troubleshooting Tips**

- **401 Unauthorized?** Double-check your PAT, username, and that your PAT has the correct scopes.
- **Private repositories:** Ensure your PAT includes the `repo` scope.
- **Multiple sources:** If you have both GitHub and nuget.org sources, specify the source explicitly if needed.

---

## **References**

- [GitHub Docs: Working with the NuGet registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-nuget-registry)
- [NuGet.Config reference](https://learn.microsoft.com/en-us/nuget/reference/nuget-config-file)

---

By following these steps, you can securely publish and consume NuGet packages using GitHub Packages, enabling streamlined collaboration and package management within your development workflow.