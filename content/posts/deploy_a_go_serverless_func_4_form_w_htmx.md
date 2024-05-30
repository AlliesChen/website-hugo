---
title: "Deploy_a_go_serverless_func_4_form_w_htmx"
date: 2024-03-30T22:33:03+08:00
# weight: 1
# aliases: ["/Deploy_a_go_serverless_func_4_form_w_htmx"]
tags: ["go", "htmx", "serverless", "deployment"]
author: "Yu-Pang"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "Desc Text."
canonicalURL: "https://allieschen.github.io/"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: false
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: false
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/AlliesChen/website-hugo/blob/main/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---

The boilplate I begin with is the production of the tutorial by BugBytes on Youtube in the video: [Golang + HTMX - Creating a Go webserver / HTMX Integration / Template Fragments](https://www.youtube.com/watch?v=F9H6vYelYyU). It’s really good and straightforward.

And I made these changes and will talk about them in this article:

- Instead of Booststrap, I used [**Tailwind CSS**](https://tailwindcss.com/) as the CSS library.
- I deployed the project on [Zeabur](https://zeabur.com/), which is a personal preference (mainly because it’s free for the demo 😅). However, feel free to choose any similar service that suits your needs.
- I use [go-chi](https://github.com/go-chi/chi) for handling routes and to server static file(stylesheet).
- I utilized the [**Go embed directive**](https://pkg.go.dev/embed) for the serverless plan on Zeabur.
- Additionally, I explored htmx a bit more for reset inputs and animation."

The demo page: https://yp-go-htmx-simpleform.zeabur.app/

The repo: https://github.com/AlliesChen/go-htmx-simpleform/tree/main

DISCLAIMER: I’m not a GoLang expert, and I’m not yet a backend developer. However, I have experience in front-end development. Any suggestions to improve the structure of the demo or to correct any misunderstandings in this article would be greatly appreciated. 🙏

## Go:embed for using files in serverless functions

To learn more about serverless functions, you can watch [this video](https://youtu.be/ofgt4r93TE4?si=RGDtalEF24iIeebc).

> Serverless function cannot access the local file system of the server. Therefore, packing static files such as templates and stylesheets is necessary to serve them as part of the response.

As Zeabur is a platform that offers free hosting for serverless functions, the remaining challange will be how to include external files, such as templates or stylesheets, in the code package.

With `go:embed`, a feature that enables embedding files into Go binaries, things couldn't be more easier.

Here is my file structure:

```
/static
 |— output.css
/templates
 |— index.html
main.go
```

Add the comments:
(PLEASE MAKE SURE NO SPACE BETWEEN THE TWO SLASH AND `go:embed` or it won't work)

```go
var (
	//go:embed templates
	templates embed.FS
	//go:embed static
	static embed.FS
	pages  = map[string]string{
		"/":          "templates/index.html",
		"/add-film/": "templates/index.html",
	}
)
```

In the handlers, rather than using `template.ParseFiles`, we focus on the folders using [`template.ParseFS`](https://pkg.go.dev/text/template#ParseFS) with **the page** which is the path to the file includes the folder name.

```go
// GET /
getPage := func(w http.ResponseWriter, r *http.Request) {
    page, ok := pages[r.RequestURI]
    // ...
    tmpl, err := template.ParseFS(templates, page)
    // ...
    if err := tmpl.Execute(w, films); err != nil {
        // ...
        return
    }
}
// POST /add-film
addFilm := func(w http.ResponseWriter, r *http.Request) {
    page, ok := pages[r.RequestURI]
    // ...
    tmpl, err := template.ParseFS(templates, page)
    // ...
    if err := tmpl.ExecuteTemplate(w, "film-list-element", Film{
        Title:    title,
        Director: director,
    }); err != nil {
        // ...
        return
    }
}
```

That's it. Execute `go run main.go` and visit http://localhost:8000/ with your browser. There should be nothing different from BugBytes' production.

## Include stylesheet built by Tailwind CSS

Similar to what we did for the HTML file in the templates directory, the serverless plan also requires embedding the stylesheet within the static folder, as previously accomplished.

In contrast to the earlier step, there is no need to parse the stylesheet. Instead, serve it directly when the website requests the */static* path:

```go
fileServer := http.FileServer(http.FS(static))
router.Handle("/static/*", fileServer)
```

This ensures that the stylesheet is readily available for use by the website without additional processing." 😊
