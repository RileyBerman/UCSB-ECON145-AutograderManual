This manual outlines how to develop an autograder for ECON 145, "Data Wrangling for Economics," at the University of California, Santa Barbara. Many of the techniques presented here can be applied to *any R-based autograder*.

Click the link in the "About" section or go to https://rileyberman.github.io/UCSB-ECON145-AutograderManual/ to access the manual. 

You can read the manual online or download the **pdf** using the toolbar's "download" icon. For the best reading experience, the online version is recommended. In the online version, code blocks are scrollable and include a copy button in the top-right corner. Code blocks may look a little wonky in the pdf version. 

This manual may contain mistakes or sections that require updates or improvement. Use the toolbar's "edit" icon to open the GitHub repository and submit issues or pull requests. 

This manual is divided as follows: 

- **Introduction**: An overview of the manual's purpose. 
- **Autograder Conventions**: An overview of the autograder system and its terminology. 
- **Public Questions**: Details about the various "Checks" used to write autograder code for Public Questions.
- **Check Flowchart**: A flowchart of Check dependencies. 
- **Putting It All Together**: Examples of complete autograder code for Public Questions.
- **Private Questions**: Details about the various functions used to write autograder code for Private Questions. 
- **DGP.R**: Information about the DGP.R file with examples. 
- **Resources**: Some additional ECON 145-specific resources. 

Special thanks to Sean Kross's ["How to Start a Bookdown Book"](https://seankross.com/2016/11/17/How-to-Start-a-Bookdown-Book.html) for his [boilerplate code](https://github.com/seankross/bookdown-start) and guidance, and to Yihui Xie's excellent book, ["bookdown: Authoring Books and Technical Documents with R Markdown"](https://bookdown.org/yihui/bookdown/), for his detailed breakdown of the bookdown package.

Collaborators can update the website by: 

1. Adding the new information to the `main` branch on GitHub via merging pull requests or directly editing the .Rmd files. 
2. Opening up a remote repository and running the lines `bookdown::render_book("index.Rmd", "bookdown::gitbook")` **and** `bookdown::render_book("index.Rmd", "bookdown::pdf_book")` in the Console or using the "Build" tab in Rstudio.
3. Committing and pushing the changes to the `main` branch on GitHub -- in particular, the `docs` folder, which contains the website files.
4. The website will automatically update shortly.

If I ever have the time (and sophistication), I will try to automate steps 2-4 with GitHub Actions.

For any questions or issues, please contact [riley.berman@gmail.com](mailto:riley.berman@gmail.com).

