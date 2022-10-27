GitHub Codespaces is a great way to spin up a development environment without having to use your own resources or install a lot of software for development.

But using Codespaces normally means you're using GitHub for source control, and we can't do that at Blue Mountain.  

However, there is a way to have the best of both worlds - store your code in BMTN's git repos and work with it in GitHub Codespaces.

The first thing to do is to [link your Microsoft Corporate account with GitHub](https://microsofteur.sharepoint.com/teams/TechBits-SP/SitePages/GitHub-link-with-MS.aspx). This allows you access to Codespaces as a Microsoft employee.

Next, Create a private repo in GitHub that has the same name as the repo you want to use in BMTN. For this example, we're using a repo called `ado-utils`. Make sure that you at least create a README file when creating the repo. GitHub won't allow you to launch a Codespaces dev container without at least SOME source code in the GitHub repo.

Now create a Codespace to work with. Click the `<> Code` button, select the `Codespaces` tab, click `New codespace`
![Screenshot 2022-04-06 210018.png](/.attachments/Screenshot%202022-04-06%20210018-ba91c740-6dd5-41b5-9415-f2f60a90d5c4.png)

Once the Codespace has completed launching, run the following commands:
```sh
ssh-keygen -C "youralias@microsoft.com"
```
Accept the defaults for generating the SSH key or assign a passphrase to the key.

Now let's get the public key that was just generated
```sh
cat /home/codespace/.ssh/id_rsa.pub
```
And copy the output from that command

Now let's prepare to receive the git Repo. To do that, let's follow step 2, 1-5 in [Use SSH key authentication](https://docs.microsoft.com/en-us/azure/devops/repos/git/use-ssh-keys-to-authenticate?view=azure-devops), being sure to copy your newly created public key as the SSH public key.

Now we need to get rid of the old git repo in the Codespace and clone the BMTN ADO repo in its stead.

```sh
cd ..
rm -rf ado-tools
git clone git@ssh.dev.azure.com:v3/BlueMountainAGMP/Blue%20Mountain%20Program%202.0/ado-tools
```
Being sure to type "yes" when asked if you want to continue connecting.

At this point you may want to stop and restart your Codespace so that it doesn't think that your source control is broken.

Now you should be able to work in GitHub Codespaces and store your source code in Blue Mountain!
