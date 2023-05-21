## Managing secrets on Kubernetes

Based on provided requirements I'll propose to consider [SOPS](https://github.com/mozilla/sops) project for encryption the values but not the keys of `json`, `yaml`, `ENV`, `INI` and many other filetypes. This functionality described on the `SOPS` project [page](https://github.com/mozilla/sops#encrypting-only-parts-of-a-file).

SOPS doesn't encrypt anything by it's nature but it uses external encryption tools like AWS KMS, GCP KMS, Azure Key Vault, age, and PGP. I personally like [age](https://github.com/FiloSottile/age) 'cause it's quite simple in configuration in comparison with PGP and completely free. With `age` we could create a pair of public and private keys and use them for encryption and decryption data by `SOPS`.

On kubernetes (for example EKS) we could use [SOPS operator](https://github.com/craftypath/sops-operator), it will automatically decrypt applied encrypted data.

### List of other solutions which I found:
- [Sealed Secrets](https://github.com/bitnami-labs/sealed-secrets). This one encrypts the whole file in comparison with SOPS but provides native kubernetes controller for secret decryption.
- [git-crypt](https://github.com/AGWA/git-crypt). Seems that this tool supports only GPG for encryption. Also this tool encrypts whole files in specified paths of repository.

