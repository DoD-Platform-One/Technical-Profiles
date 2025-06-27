# File structure
- The `base` directory has reusable components to provide common values to every profile like registry credentials, Flux values, and helm repo configurations.
- Each technical profile directory contains specific resources and Kustomize overlays needed to successfully deploy Big Bang using that profile.

## Technical Profile Directories

Each technical profile directory will have the following file structure:
#### bigbang.yaml (Required)
  - This defines the Flux GitRepository resource which points to your remote profile git repository.
  - Credentials for private repos are provided using sops.
#### kustomization.yaml (Required)
  - This defines which resources are part of the profile and how they should be layered together for the deployment.
#### secrets.enc.yaml (Optional)
  - If any components of the profile require secret values, there will be a secrets.yaml.template file showing which fields need to be populated.
#### Additional YAML files (Optional)
  - Additional resources like configmaps or other secrets can be added to the profile directory if needed to support other applications.
  - These files will already be present in the predefined profiles but the values may need to be updated.
  - If you need to add these resources for your custom deployment:
    - Add the YAML file to the profile directory.
    - Add the YAML file to the kustomization.yaml resources list.


# BBCTL

> **Warning**: Technical profiles are actively being developed. Not all of the following commands are implemented in bbctl and everything in this section is subject to change!

## Setting up a profile
- Fork the technical profiles repository and add it to your own remote repository.
- Add the path to the local checkout of the repository to your bbctl yaml config file in the field `tech-profile-repo`.
- Run `bbctl profile init <profile-name>`:
  - This will parse out the files in the given profile directory and provide an interactive way to populate required values.
  - You can configure the sops credentials with this command.
    - The secret.enc.yaml file will be created or updated using this information.
  - You will be prompted to fill in missing values in additional files like secret.enc.yaml, configmap.yaml, etc. as well.

## Deploying a profile
> **Note**: bbctl will assume all missing config values have been provided and will deploy the files exactly as they appear in the profile directory!

- Run the command `bbctl deploy bigbang --profile=<profile-name>` with the directory name of the profile to deploy.
- bbctl will use Kustomize to pull all the profile resources together and deploy them into the cluster currently referenced by the KUBECONFIG environment variable.

## Updating a Profile
- Profiles may be updated using `bbctl profile update <profile-name>`
  - This will pull in the latest changes to that profile from this repository.
  - This may result in merge conflicts with your custom changes.
- Changes to the base directory must be done using a separate command `bbctl profile update base`
