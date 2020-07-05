## 1.	High Level Summary

When performing reconnaissance and enumeration steps, there are several vulnerabilities identified on the Lame machine that can be used to gain access to the target.
•	Samba
Samba with version smbd 3.0.20-Debian has a vulnerability and was recorded in CVE 2007-2447, we use this vulnerability to do a reverse shell and gain root access on the target machine.
•	Distcc
With the nmap distcc-cve2004-2687.nse script, we use it to create a reverse shell and it will get the user daemon. we need to escalate privilege that user, and that can be done with vulnerabilities in linux kernel 2.6 that are identified on the target machine

-------------------------------------------------------------------------
## Welcome to Lame Pages

You can use the [editor on GitHub](https://github.com/imamrahman15/OSCP_preparation/edit/master/README.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/imamrahman15/OSCP_preparation/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
