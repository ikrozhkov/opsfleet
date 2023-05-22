## Managing secrets on Kubernetes

Based on provided requirements I'll propose to consider [SOPS](https://github.com/mozilla/sops) project for encryption the values but not the keys of `json`, `yaml`, `ENV`, `INI` and many other filetypes. This functionality described on the `SOPS` project [page](https://github.com/mozilla/sops#encrypting-only-parts-of-a-file).

SOPS doesn't encrypt anything by it's nature but it uses external encryption tools like AWS KMS, GCP KMS, Azure Key Vault, age, and PGP. I personally like [age](https://github.com/FiloSottile/age) 'cause it's quite simple in configuration in comparison with PGP and completely free. With `age` we could create a pair of public and private keys and use them for encryption and decryption data by `SOPS`.

On kubernetes (for example EKS) we could use [SOPS operator](https://github.com/craftypath/sops-operator), it will automatically decrypt applied encrypted data.

### List of other solutions which I found:
- [Sealed Secrets](https://github.com/bitnami-labs/sealed-secrets). This one encrypts the whole file in comparison with SOPS but provides native kubernetes controller for secret decryption.
- [git-crypt](https://github.com/AGWA/git-crypt). Seems that this tool supports only GPG for encryption. Also this tool encrypts whole files in specified paths of repository.

___

## Secrets in AWS Secret Manager

If the requirement is to store secrets in AWS Secret Manager and then mount to the EKS cluster's PODs I would suggest to use [Secrets Store CSI Driver](https://secrets-store-csi-driver.sigs.k8s.io/) and [AWS provider](https://github.com/aws/secrets-store-csi-driver-provider-aws) for it.

As prerequisite we'll need an IAM policy with permissions to retrieve a secret from the Secrets Manager and Service Account associated with that IAM policy. In the namespace where is the POD with mounted secret located we should also create `SecretProviderClass` Object.

The detailed instructions described on [AWS documentation page](https://docs.aws.amazon.com/secretsmanager/latest/userguide/integrating_csi_driver.html)

## Secrets in Hashicorp Vault

Similar to AWS Secret Manager approach we could use [Secrets Store CSI Driver](https://secrets-store-csi-driver.sigs.k8s.io/) and [Vault provider](https://github.com/hashicorp/vault-csi-provider)

The flow will be pretty similar but `SecretProviderClass` Object will be configured slightly different. The official guide is located [here](https://developer.hashicorp.com/vault/docs/platform/k8s/csi) 

Alternatively we can use [Vault Agent](https://developer.hashicorp.com/vault/docs/platform/k8s/injector) sidecar containers that render Vault secrets to a shared memory volume. But this approach is vendor-specific opposed to `Secrets Store CSI Driver`.