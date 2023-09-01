--- 
title: "How to Learn Git, Markdown and PowerShell by Contributing to the PowerShell-Docs Repository" 
date: 2023-03-17T08:23:11+01:00 
draft: false
comments: true

---

{{< figure
  src="/pics/docs_cont.png"
>}}

## Intro

The PowerShell-Docs repository is the home of the official PowerShell documentation. It contains reference and conceptual content for various versions and modules of PowerShell. Contributing to this repository is a great way to learn Git, Markdown and PowerShell, as well as to help improve the quality and accuracy of the documentation.

In this blog post, I will show you how you can contribute to the PowerShell-Docs repository by doing quality contributions, and why it’s beneficial for your learning and development.

## What are quality contributions?

Quality contributions are enhancements or fixes that improve the readability, consistency, style or accuracy of the documentation. They can include things like:

- Adding [alias notes](https://learn.microsoft.com/powershell/scripting/community/contributing/quality-improvements?view=powershell-7.2#aliases) for cmdlets
- [Formatting code](https://learn.microsoft.com/powershell/scripting/community/contributing/quality-improvements?view=powershell-7.2#formatting-code-samples) samples and command syntax
- Updating [link references](https://learn.microsoft.com/powershell/scripting/community/contributing/quality-improvements?view=powershell-7.2#link-references)
- Fixing [spelling](https://learn.microsoft.com/powershell/scripting/community/contributing/quality-improvements?view=powershell-7.2#spelling) errors
- Applying [Markdown linting](https://learn.microsoft.com/powershell/scripting/community/contributing/quality-improvements?view=powershell-7.2#markdown-linting) rules


Quality contributions are different from content contributions, which involve adding new articles or topics, or making significant changes to existing ones. Content contributions require more discussion and approval from the PowerShell-Docs team before they can be merged.

## How to make quality contributions?

Before we get into how to make quality contributions, I'd like to shamelessly plug my own module: [PowerShell-Docs-CommunityModule](https://www.powershellgallery.com/packages/PowerShell-Docs-CommunityModule/)
It will help you pick out work that has not been done yet.

Install & try it, using the following code:

```powershell
Set-Location $env:USERPROFILE

#  Make sure 'username' reflects your actual github username
git clone https://github.com/username/PowerShell-Docs

Install-Module PowerShell-Docs-CommunityModule

Find-MissingAliasNotes -Verbose
```

To make quality contributions, you need to have a GitHub account and some basic knowledge of Git and Markdown. You also need to install some tools that will help you edit and preview your changes locally. Here are some steps you can follow:

1. Fork the PowerShell-Docs repository on GitHub.
2. Clone your forked repository to your local machine using Git.
3. Install Git, Markdown tools, Docs Authoring Pack (a VS Code extension), and Posh-Git (a PowerShell module).
4. Check out the PowerShell Docs Quality Contributions project on GitHub. This project tracks all the open issues and PRs related to quality improvements.
5. Pick an issue that interests you or create a new one if you find something that needs fixing. You can use PowerShell-Docs-CommunityModule to help you here.
6. Assign yourself to the issue and start working on it locally using VS Code or your preferred editor. Make sure you [create a new branch](https://www.atlassian.com/git/tutorials/using-branches) before editing any files. Making a new branch will ensure your edited files is clean in your upcoming Pull-Request.
7. Preview your changes, make sure you've edited the `ms.date` at the top of the document to todays date (MM/dd/yyy), this way other contributors know when the document has been edited, it's also required when doing an update, so the owners of the repository will ask you to do this if you miss it. 
8. Commit your changes using Git and push them to your forked repository on GitHub.
9.  Create a pull request (PR) from your forked repository to the original PowerShell-Docs repository on GitHub.
10. Wait for feedback from reviewers or maintainers of the PowerShell-Docs team.
11. Address any comments or suggestions they may have until your PR is approved and merged.



## Why make quality contributions?
Making quality contributions has many benefits for both you and the PowerShell community.

### For you:

- You can learn Git, by contributing to a very friendly large code-base project. The owners are more then willing to help you with git related questions. You'll grow a ton in this area once you start doing some PRs.
- You will write/edit files in Markdown (.md), a very popular markup language.
- Because you will be proof-reading the markdown documents, you will learn more PowerShell topics straight from the source of the documentation.
- You can improve your writing skills by following Microsofts style guides and best practices for technical documentation.
- You can get feedback from experts who work on PowerShell, Markdown and Git every day.
- You can build your reputation as a contributor by having your name appear in commit history.

### For the community:

- You can help improve the clarity, consistency, style or accuracy of the documentation that many people rely on every day.
- You can help reduce confusion, errors or frustration among users who read the documentation.
- You can help keep the documentation up-to-date with changes in PowerShell features or 
functionality.
- You will be listed in the [Community Contributor Hall of Fame](https://learn.microsoft.com/powershell/scripting/community/contributing/overview?view=powershell-7.2)


## Conclusion

In this post, I showed you how you can contribute to the PowerShell-Docs repository by doing quality contributions, and why it’s great for learning Git, Markdown and PowerShell, while at the same time using the PowerShell-Docs-CommunityModule to find out what to do first. We hope this blog post inspires you to join us in making the PowerShell documentation better for everyone.

If you have any questions, comments, or suggestions, please feel free to reach out on a DM either on twitter, email or mastodon!
I hope to see a PR from you, and if you've successfully done so because of this post, make sure to notify me about it! 😃


## Happy contributing