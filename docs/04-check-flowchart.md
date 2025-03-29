# Check Flowchart {#Check-Flowchart}

The flowchart below outlines the recommended order for [General](#General-Checks) and [Special Checks](#Special-Checks) when building the autograder script for a Public Question. 

Red arrows flowing to a Check indicate its **dependency on previous Checks.**

:::: {.infobox .note data-latex="{bell_note}"}

**Note**: 

This sequence is flexible. Experienced TAs can reorder Checks to suit the logic of a specific question -- *so long as dependencies are preserved*. 

::::

\newpage 


```{=html}
<div id="htmlwidget-610e94d139f5240884d9" style="width:672px;height:1000px;" class="grViz html-widget"></div>
<script type="application/json" data-for="htmlwidget-610e94d139f5240884d9">{"x":{"diagram":"digraph {\n  \ngraph[layout = dot, rankdir = TB]\n\nnode [fontname = Verdana, shape = ellipse, style = rounded, height = 1.5, width = 1.5, style = filled, fillcolor = Orange]\n\nCheck1 [label = \"Prerequisite \n Check\", fillcolor = LightBlue]\n\nCheck2 [label = \"Name \n Check\", fillcolor = LightBlue]\n\nCheck3 [label = \"Structure \n Check\", fillcolor = LightBlue]\n\nCheck4 [label = \"Column Name \n Check\", fillcolor = LightBlue]\n\nCheck5 [label = \"Type \n Check\"]\n\nCheck6 [label = \"Expression \n Check\"]\n\nCheck7 [label = \"Calculation \n Check\"]\n\nCheck8 [label = \"Value \n Check\"]\n\nCheck9 [label = \"NA \n Check\"]\n\nCheck10 [label = \"Row & Column \n Check\", fillcolor = LightBlue]\n\nCheck11 [label = \"Correct \n Check\", fillcolor = LightBlue]\n\nCheck1 -> Check2 \n\nCheck2 -> Check3 [color = red3]\n\nCheck3 -> Check4 [color = red3]\n\nCheck4 -> Check8 [color = red3]\n\nCheck4 -> {Check5, Check6, Check9} [color = red3]\n\nCheck5 -> Check7 [color = red3]\n\n{Check7, Check6, Check9} -> Check8\n\nCheck8 ->  Check10\n\nCheck10 -> Check11 [color = red3]\n\nColExplanation1 [label = \"\\lGeneral Check\", shape = plaintext, fontcolor = LightBlue, width = 0, height = 0, fillcolor = Transparent, fontname = \"Verdana-Bold\"]\n\nColExplanation2 [label = \"\\lSpecial Check\", shape = plaintext, fontcolor = Orange, width = 0, height = 0, fillcolor = Transparent, fontname = \"Verdana-Bold\", align = \"left\"]\n\n{rank = same; Check5; ColExplanation2}\n\n}","config":{"engine":"dot","options":null}},"evals":[],"jsHooks":[]}</script>
```
