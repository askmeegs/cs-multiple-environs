#  Using ConfigSync with Multiple Environs 

This guide shows you how to set up Config Sync for GKE across two Environs, dev and prod, using config management best practices. 

## Overview 

![](screenshots/architecture.png)


## Prerequisites 

- 2 Google Cloud projects
- kubectx tool 
- nomos tool 
  
## Steps 

1. Set variables 

```
TODO
```

1. Create 1 cluster in each of the 2 projects. 

```
./1-create-clusters.sh
```

2. Register clusters to separate Anthos environs. 

```
./2-register-clusters.sh
```

3. Create 3 github repos. 

```
./3-create-repos.sh
```

4. Create secret manager secrets in the prod project with your Git credentials. This will allow Cloud Build to push to Github on your behalf. 

```
./4-secret-manager-git.sh
```

5. Open the Cloud Console in your prod project, and navigate to Cloud Build. Click Triggers > Manage Repositories > Connect Repository. Check the `foo-config-source` repo, then click **Done.** 


6. From the Cloud Build dashboard, create a Trigger from the `foo-config-source` repo with the following fields: 

- Trigger name: Foo-Config-Hydration
- Event: push to a new branch
- Repository: foo-config-source
- Branch: ^main$
- Type: Autodetected (cloudbuild.yaml)


Click **Create**. 

7. Because we already pushed to the foo-config-source repo before creating this trigger, let's run it manually to trigger the hydration of the dev and prod repos. In the triggers list, in the `Foo-Config-Hydration` row, click **Run** on the right side of the screen. The build should run successfully, writing the output of `kustomize build` to the `foo-config-dev` and `foo-config-prod` repos, respectively. 

![](screenshots/build-success.yaml)

8. Once the build completes, open one of the dev or prod repos. You should see YAML files populating the repo, and a README update indicating the commit SHA of the `foo-config-source` repo that this repo was last built from. 


![screenshot](screenshots/git-output.yaml)


9. Run `nomos status`. You should see that both your dev and prod clusters are now `synced` to their respective repos. 

```

```

10. Switch to the `dev` cluster. Get namespaces to verify that the resources are synced - you should see a `foo` and `bar`  namespace appear. 


```
kubectl get namespace 
```

Expected output: 

```

```