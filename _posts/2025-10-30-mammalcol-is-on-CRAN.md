---
title: "My first R package is on CRAN!"
show_date: true
toc: true
category: 
  - English
tags: 
  - R
  - Package
  - Mammal
header:
  teaser: "/images/mammalcol/mammalcol_to_cran.png"
  overlay_image: /images/texture-feature-08.jpg
  caption: "Photo: [Mountain Tapirs, Los Nevados National Park. Diego J. Lizcano](https://www.instagram.com/walking_tapir/)"
comments: true
share: true
last_modified_at: 2025-10-30T01:24:36-0400
---


# mammalcol is on CRAN!

I woke up this morning to an email I wasn't quite expecting — `mammalcol`, my R package for exploring the mammals of Colombia, had been accepted to CRAN. I sat there for a moment, coffee in hand, feeling a mix of relief and quiet pride. It's been quite a journey to get here.

![BirdNET-GO Dashboard](/images/mammalcol/logo_mammalcol.png) 

## It started with a messy camera trap dataset

Like most things I build, `mammalcol` wasn't planned. It grew out of necessity. I was working through a large camera trap dataset, trying to put proper species names and details to hundreds of detections. I kept writing the same kinds of helper functions over and over — little snippets to look up species info, check taxonomy, pull distributions. Eventually those snippets became functions, and the functions found a home in a personal package that lived only on my computer, tangled up with a bunch of other stuff that was probably only useful to me.

When a manuscript started taking shape from that analysis, I hit a wall. My analysis code loaded my personal package, which meant no one else could realistically reproduce my work. I needed a better solution.

## Pulling it apart and making it shareable

The answer came from looking at what others had done in similar situations. I should separate out the useful bits, clean them up, and publish them properly. So that's what I did — I pulled the relevant functions into their own standalone package, invited some other people who care about Colombian mammals and R to join in, and `mammalcol` started to take shape as something meant for the world, not just for me.

That process of going from "functions on my computer" to "package on CRAN" was honestly more painful than I imagined. I'd heard it was tricky, but I underestimated just how much attention to detail CRAN submission requires. Several rounds of submissions. Notes coming back about small things — a period missing here, a line too long there, a word in the description that CRAN didn't like. Each time I'd fix the notes and resubmit, half expecting another round of feedback.

A few things that helped keep me sane through the process:

- **`usethis`** — an incredible package that automates so much of the scaffolding involved in building and maintaining an R package. I leaned on it constantly.
- **The R Packages book by Hadley Wickham** — I followed it closely to get the first draft off the ground. A genuinely great guideline.
- **`testthat`** — writing tests felt like extra work at first, but it saved me from breaking things more times than I can count.

## And then, the email

After all of that, the acceptance email felt almost anticlimactic in the best possible way. A small, quiet moment at the start of an ordinary morning. But I know how much effort went into it — from me and from the collaborators who joined the project and helped shape it into something more than I could have built alone.

The package isn't flashy. It does a humble but useful job: giving researchers quick, reliable access to the list of mammal species of Colombia, with tools to search, map, and validate occurrence data. Colombia is one of the world leaders in mammal diversity, and I hope `mammalcol` makes it a little easier for people to explore that.

A manuscript is still in the works, which is what started all of this in the first place. But for now, I'm just glad the package is out there.

If you want to try it:

```r
install.packages("mammalcol")
```



> And if you find a bug, have a suggestion, or spot a species in a new departamento — I'd love to hear from you. The [GitHub repo](https://github.com/dlizcano/mammalcol) is the best place to reach out.
{: .notice--warning} 


<p>
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="http://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
</p>
