--- 
title: "UCSB ECON 145 Autograder Manual"
author: "Riley Berman, Alex Zhao, & 2024 ECON 145 Summer Team"
date: 'Last Updated: 2025-03-29'
site: bookdown::bookdown_site
documentclass: book
bibliography:
- book.bib
- packages.bib
colorlinks: true
fontsize: 12pt
description: "This manual outlines how to develop an autograder for UCSB ECON 145 coding assignments. Many of the techniques presented here can be applied to any R-based autograder."
url: 'https\://rileyberman.github.io/UCSB-ECON145-AutograderManual/' 
link-citations: true
always_allow_html: true
tags: [Tutorial, Manual, Autograder, R Programming]
---

# Preface {-}

This manual outlines how to develop an autograder for ECON 145, "Data Wrangling for Economics," at the [University of California, Santa Barbara](https://econ.ucsb.edu/programs/undergraduate/courses). Many of the techniques presented here can be applied to *any R-based autograder*.

You can read the manual online or download the **pdf** using the toolbar's "download" icon. For the best reading experience, the online version is recommended. In the online version, code blocks are scrollable and include a copy button in the top-right corner. Code blocks may look a little wonky in the pdf version. 

This manual may contain mistakes or sections that require updates or improvement. Use the toolbar's "edit" icon to open the GitHub repository and submit issues or pull requests. 



## Acknowledgements {- #Acknowledgements}

This guidebook aims to reflect new conventions and up-to-date techniques used in ECON 145 autograders developed by the 2024 ECON 145 Summer Team of **Jack Keefer (Head TA), Alex Zhao, Riley Berman, Shreya Sinha, and Michal Snopek**. We are extremely grateful for the foundational work established by previous ECON 145 TAs.

Special thanks to Alex Zhao and his excellent `autograder_manual_2024.pdf`, which  laid the groundwork for this manual.

The author would also like to thank Xiao Yang for her invaluable feedback and suggestions. 

\BeginKnitrBlock{flushright}<p class="flushright">Riley Berman</p>\EndKnitrBlock{flushright}
