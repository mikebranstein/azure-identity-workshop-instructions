## Connecting the app to Azure

### resource Group
- create resource group

### Storage account
- create storage account
    - name (remember it)
    - deployment model: resource manager
    - Account kind: general purpose
    - Performance: standard
    - Replication: Locally-redundant storage (LRS)
    - Storage service encryption: Disabled
    - Resource group: Use existing (from the prrevious)
    - Location: East US

- Wait for it to deploy

### Connect to it from storage explorer
- add subscription azure storage explorer
- select the subscription
- find the sotrage account
- right-cick, copy Primary key
- Update connection string
- Account name: what you entered above
- Account key: what you copied from storage explorer

### Re-run and test!
- register a new account, test out the site

### now you have a cloud-based central authentication database
- highly-scalable
- can be encrypted
- can use it across applications
