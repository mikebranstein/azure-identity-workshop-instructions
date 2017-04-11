## Introducing a staging area for images

We really don't want to allow employees to upload ANYTHING into their profile picture....

### update web app to place code into a staging area

- update Index.cshtml verbiage for alt img tag
- update manage controller code for UploadImageAsync
- add web.config app setting for ProfilePicUploadBlobContainer

### view results

- run project
- upload an image
- look at storage explorer and see image in the uploaded directory

