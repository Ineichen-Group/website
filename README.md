This is the code for STRIDE-Lab website that can be accessed here: https://ineichen-group.github.io/website/

- [Quarto Website Setup](#quarto-website-setup)
  - [1. \_quarto.yml](#1-_quartoyml)
  - [2. .qmd files](#2-qmd-files)
    - [Simple Pages](#simple-pages)
      - [YAML Front Matter](#yaml-front-matter)
      - [Flexible Box (Flexbox) Layout](#flexible-box-flexbox-layout)
      - [Sections with text](#sections-with-text)
    - [Advanced pages](#advanced-pages)
    - [3. \_ejs](#3-_ejs)
    - [4. styles.css](#4-stylescss)
- [How-to Make Updates](#how-to-make-updates)
  - [Modifying existing content](#modifying-existing-content)
  - [Adding new content (news, people, publications, research areas)](#adding-new-content-news-people-publications-research-areas)
      - [Setup local git repository](#setup-local-git-repository)
      - [Make changes](#make-changes)
      - [Commit and push changes](#commit-and-push-changes)
  - [Adding Publications](#adding-publications)
  - [Adding People](#adding-people)
- [Continuous Integration with GitHub](#continuous-integration-with-github)
    - [Image](#image)
    - [Pages Stage](#pages-stage)
    - [Before Script](#before-script)
    - [Script](#script)
    - [Artifacts](#artifacts)
    - [Only](#only)
    - [Interruptible](#interruptible)
- [Reference GitHub Pages](#reference-github-pages)

# Quarto Website Setup
The website content is in [quarto_docs](quarto_docs). 

## 1. _quarto.yml
Every website has a [_quarto.yml](quarto_docs%2F_quarto.yml) config file that provides website options as well as defaults for HTML documents created within the site, e.g.:
  - the project type (website)
  - website navigation (top or side navigation bars, page footer etc.), see [docu](https://quarto.org/docs/websites/website-navigation.html)
  - formatting details (pointers to the website theme, custom css, setting of a main font), see [docu](https://quarto.org/docs/output-formats/html-themes.html)
  - other metadata, such as Twitter Cards to provide an enhanced appearance when someone links to your site on Twitter, see [docu](https://quarto.org/docs/websites/website-tools.html#social-metadata)
  
## 2. .qmd files
Quarto .qmd files contain a combination of markdown and executable code cells. They are processed by Quarto to generate the final output of the web page content.

### Simple Pages
In the context of this project, [index.qmd](quarto_docs%2Findex.qmd) (main page), [join.qmd](quarto_docs%2Fjoin.qmd) and [contact.qmd](quarto_docs%2Fcontact.qmd) represent the full content for a single page of the website.

Below as an example is an explanation for the functioning of the [join.qmd](quarto_docs%2Fjoin.qmd) file.

#### YAML Front Matter
At the top the file there is a YAML block with document level options.

```
---
title: "Join the Team"
description: "Interested in joining the lab?"
image: "images/join.png"
format:
  html:
    page-layout: full
---
```
- **Title:** Sets the title of the page, which appears in the browser's title bar and as a heading on the page.
- **Description:** Provides a meta description for the page that helps with SEO and gives a brief overview of the page content.
- **Image:** Specifies a path to an image that might be used as a banner or featured image for the page.
- **Format/HTML/Page-Layout:** Directs Quarto to use a full-page layout for the HTML output, influencing the CSS to utilize the full width of the viewport.

#### Flexible Box (Flexbox) Layout

```
::: {style="display:flex; align-items:center; gap:25px;"}
![](images/join_us-01.png){width="628"}

Do you want to contribute to enhancing welfare of research animals or to the development of novel drugs for neurological diseases? [Get in touch!](contact.qmd)
:::
```
Utilizes the `:::` notation to create a custom div block with specific styling:
- **Display:** `flex` - The content inside the div is laid out using CSS Flexbox for a flexible and responsive design.
- **Align-Items:** `center` - Vertically aligns the items in the center of the container.
- **Gap:** `25px` - Sets a gap of 25 pixels between each item in the flex container.

Within this styled div:
- **Image:** Displays an image (`![](images/join_us-01.png){width="628"}`) with a specified width of 628 pixels, intended to visually complement the call to action.
- **Text and Link:** Encourages the reader to get in touch via a linked `.qmd` file (`contact.qmd`).

#### Sections with text
The "##" symbols designate section headers within the document, such as:
```
## Open job positions
We are looking for Master students from the field of (bio)medicine and/or computer science. Get in touch to discuss potential projects!
```

### Advanced pages 

The sections for [about](quarto_docs%2Fabout), [events](quarto_docs%2Fevents), [news](quarto_docs%2Fnews), [people](quarto_docs%2Fpeople) and [publications](quarto_docs%2Fpublications) are constructed from a single .qmd file that outlines the overall structure of the content, along with several subfolders containing additional .qmd files. 
These files serve as individual pages that detail specific items, such as single events or publications.

Below is an example of the about/[index.qmd](quarto_docs%2Fabout%2Findex.qmd) file:

```
---
title: About STRIDE-Lab
description: "Learn about our research topics."
image: "../images/about.png"
listing:
  id: research
  type: grid
  grid-columns: 4
  template: ../_ejs/research-areas.ejs
page-layout: full
toc: false
---
```
Here’s a breakdown of the new elements:
- **listing**: 
  - id: listing id, which one can use to reference this list outside of the YAML block.
  - type: Sets the layout type for the content, which is a grid layout.
  - grid-columns: Specifies that the grid should have 4 colum[ns.
  - template: Points to a template file ([research-areas.ejs](quarto_docs%2F_ejs%2Fresearch-areas.ejs)) that defines how each element in the grid should be displayed. 
- **toc**: table of contents is set to false

Additionally, multiple lists with distinct conditions can be defined. For instance, in the example below, 
there are two event lists—one for active events and another for past events—categorized by their "event_status", see [index.qmd](quarto_docs%2Fevents%2Findex.qmd).
```
---
title: Events
description: "Join us on one of our events, or learn about our participations."
image: "../images/events.png"
listing:
  - id: active_events
    include: 
        event_status: "active"
    type: grid
    grid-columns: 3
    template: ../_ejs/event-box.ejs
  - id: past_events
    include: 
        event_status: "past"
    type: grid
    grid-columns: 3
    template: ../_ejs/event-box.ejs
page-layout: full
---
```
The "event_status" field must be specified in the YAML section of the .qmd files for the individual events within the respective subfolders. 
Here is an example of the .qmd file for one event, including the embedding of a PDF flyer:

``````
---
title: "STRIDE-Lab & Norecopa Summer School"
description: "Systematic reviews of animal studies for evidence-based preclinical research."
alias: &ALIAS translation
event_date: "20-23 August 2024"
event_status: "active"
image: featured_active.png
toc: false
---

## Related Information

```{=html}
<object data="flyer.pdf" type="application/pdf" width="100%" height="100%" style="min-height:100vh;">
    <p>It appears you don't have a PDF plugin for this browser.
    No biggie... you can <a href="flyer.pdf">click here to
    download the PDF file.</a></p>
</object>
```
``````

The lists can be then invoked using the "#"-sign follows:

```
## Upcoming Events
::: {#active_events}
:::

## Past Events
::: {#past_events}
:::
```
### 3. _ejs
Directory containing Embedded JavaScript (EJS) templates. Those are used to customize the structure of the output documents, integrating JavaScript for more advanced layouts and dynamic functionalities in the rendered outputs.
As we saw above, the events page uses the template [event-box.ejs](quarto_docs%2F_ejs%2Fevent-box.ejs), which is defined as follows:

``````
```{=html}
<div class="quarto-listing quarto-listing-container-grid">
  <div class="list grid quarto-listing-cols-3">
    <% for (const item of items) { %>
      <div class="g-col-1 listing-box">
        <a href="<%- item.path %>" role="button" class="quarto-grid-link">
          <div class="quarto-grid-item card h-100 card-left">
            <p class="card-img-top">
              <img alt="<%= item.title %>" src="<%= item.image %>" class="thumbnail-image card-img listing-box-image" style="height: 200px;">
            </p>
            <div class="card-body post-contents">
              <div class="listing-box-title"><%= item.title %></div>
              <div class="listing-box-subtitle">
                <% if (item.event_date) { %>
                  <%= item.event_date %>
                <% } %>
              </div>
            </div>
          </div>
        </a>
      </div>
    <% } %>
  </div>
</div>
```
``````
This template uses a combination of HTML and server-side template syntax to render a responsive, interactive listing of items 
in a grid format, where each item is represented by a styled card that includes an image and relevant textual details. 
The components such as item.title and item.event_date are refer to the definitions of the contents for each event. 

### 4. styles.css
[styles.css](quarto_docs%2Fstyles.css) is a CSS file for customizing the appearance of the output documents. 
It imports various fonts and sets styles for webpage elements to enhance typography and layout. 
The file also includes responsive design adjustments for different screen sizes, particularly for navigation elements. Hover effects, padding, and text styling are specified to improve visual interaction and readability.

# How-to Make Updates

## Modifying existing content (depricated #TODO: update to GitHub)
If you want to modify a single file with simple changes, e.g. adding a few new sentences, fixing typos, changing the tag to a different category, the easiest way is in the browser.
1. Navigate to the file that you want to change and click on it, navigate to "Edit single file".
![1.select_file_gitlab.png](docu_screenshots%2F1.select_file_gitlab.png)
![2.edit_single_file.png](docu_screenshots%2F2.edit_single_file.png)
2. You will now see the editing view of the file and can make the desired changed.
![3.edit_file_view.png](docu_screenshots%2F3.edit_file_view.png)
3. Write a meaningful commit message describing the changes you made, and commit the changes.
![4.commit_edit.png](docu_screenshots%2F4.commit_edit.png)
4. When you go back to the repository, you will see the progress bar of a running deployment.
After a few seconds-minutes it should turn green for a successful deployment and the changes should be visible on the website.
![5.deployment_running.png](docu_screenshots%2F5.deployment_running.png)
![6.deployment_done.png](docu_screenshots%2F6.deployment_done.png)

## Adding new content (news, people, publications, research areas)
To add new content, it is best to work with a clone of the github repository. There are many ways to work with Quarto and a common one is using RStudio. [Here](https://quarto.org/docs/get-started/hello/rstudio.html) you can get instructions to get started. The basic steps are:

#### Setup local git repository
1. If not already authenticated, make sure you have a connection to the GitHub repository via the terminal. Here an example for authentication via a personal access token:
   1.1. Create a personal access token, see instructions [here](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token). 
   1.2. Copy the token and use it in your terminal to establish connection to the repository

    ```
    git remote set-url origin https://oauth2:<your_personal_access_token>@github.com/Ineichen-Group/website.git
    ```  

2. Clone the repository locally, e.g via HTTPS:
 ```
git clone https://github.com/Ineichen-Group/website.git
```  

#### Make changes
1. Install RStudio.

2. Be sure that you have installed the tidyverse and palmerpenguins packages. Additionally, we use the leaflet package to display the map of Zurich in Contacts page:
```
install.packages("tidyverse")
install.packages("palmerpenguins")
install.packages('leaflet')
```

1. Open as project the folder of containing the website content.

2. Make the needed updates, e.g., by opening a .qmd file in RStudio or creating a new folder and adding new files.

3. Test the changes by running from the button "Render" or with the command in the Console:
```
quarto::quarto_preview()
```
Please make sure you test the change locally before committing! You can get more detailed error messages and it is easier to debug compared to the GitHub Actions log.

#### Commit and push changes
```
git add <path to changed file or foder>
git commit -m "message explaining the change"
git push
```

The easiest approach for adding new content for news, people, publications and research areas is to duplicate the folder of an existing element, rename it, and modify its contents as needed.

As long as the keys in the new .qmd file remain unchanged and only the values are modified, the element will be read correctly.

## Adding Publications
When adding a new publication, we would need to update these elements:

- **title**: The title of the publication.
- **date**: The date when the document or entry was created.
- **author**: The list of authors who contributed to the publication. The same author_id should be used as in the people's author_id field.
- **publication**: The journal or conference where the work was published, including volume and article number.
- **publication_type**: The type of publication, indicated by a code ( "O" for Original article and "R" for Review).
- **categories**: Keywords or categories that describe the main topics of the publication. Available categories are "DS" (for Data Science), "MRI", "SR" (for Systematic Review), "Translation".
- **url_source**: The URL or DOI link to access the publication online.
- **year**: The year of publication.
- **image**: The filename of an associated image, often used for visual representation.
- **pub_number**: An identifier or number assigned to the publication for reference purposes.

```
---
title: "Magnetic resonance imaging in multiple sclerosis animal models: A systematic review, meta-analysis, and white paper."
date: 2022-05-01
author: ["Ineichen BV", "Sati P", "Granberg T", "Absinta M", "Lee NJ", "Lefeuvre JA", "Reich DS"]
publication: "NeuroImage Clinical, 28:102371. Epub 20200802. 2020."
publication_type: "O"
categories: ["MRI", "SR", "Translation"]
url_source: https://doi.org/10.1016/j.nicl.2020.102371
year: 2020
image: featured.jpeg
pub_number: 11
---
```

## Adding People

When adding a new person, we would need to update these elements:
- **Title, Last, First, and Author ID**: Update with the new person's full name and unique identifier. The unique identifier should be the same used in the authors list of publications.
- **Subtitle**: Enter the person's current job title or role.  
- **People Group**: This determines the list to which the person will be attributed in the people overview page. 
Available categories are "research-assistant", "pi", "postdoc", "alumni", "master-thesis", "phdstudent" and of course "teddy-bears".
- **Email**: Provide the new person's professional email address.
- **Education**: List the new person's educational qualifications.
- **Image**: Make sure the image in the folder is replaced with a new avatar image if available.
- **Links** (in the `about` section):
  - Ensure the email link (`href`) is updated to the new person’s email.
```
---
title: Pia Härvelid
last: Härvelid
first: Pia
author_id: &TITLE Haervelid P
subtitle: "Research Assistant"
people_group: "research-assistant"
email: "pia.haervelid@hotmail.com"
education:
  - MA Medicine | Università della Svizzera italiana  <br> BSc Human Medicine | ETH Zurich
description: ""
image: avatar.png
page-layout: full
listing:
  id: pubs
  template: ../../_ejs/publications-people.ejs
  contents: ../../../publications/**/*.qmd
  sort: "pub_number desc"
  # sort-ui: true
  filter-ui: true
  include:
    author: *TITLE
  fields: [publication, title, categories, image, date, author]
about: 
  id: about
  template: trestles
  image-shape: round
  image: avatar.png
  links:
    - icon: envelope
      text: Email
      href: mailto:pia.haervelid@hotmail.com 
---
```

# Continuous Integration with GitHub
This project's static Pages are built by GitHub Actions and served via GitHub Pages. The workflow is defined in [`.github/workflows/quarto-publish.yml`](.github/workflows/quarto-publish.yml):

```
name: Build and Publish Quarto Site

on:
  push:
    branches: [ main ] 

jobs:
  build-deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Set up R
        uses: r-lib/actions/setup-r@v2

      - name: Install system dependencies
        uses: r-lib/actions/setup-r-dependencies@v2
        with:
          packages: |
            quarto
            leaflet

      - name: Set up Quarto
        uses: quarto-dev/quarto-actions/setup@v2

      - name: Render Quarto site (via R)
        run: |
          cd quarto_docs
          Rscript -e "quarto::quarto_render()"

      - name: Upload site artifact for GitHub Pages
        uses: actions/upload-pages-artifact@v3
        with:
          path: quarto_docs/_site

  deploy:
    needs: build-deploy
    runs-on: ubuntu-latest
    permissions:
      pages: write
      id-token: write
    environment:
      name: github-pages
      url: ${{ steps.deploy.outputs.page_url }}

    steps:
      - name: Deploy to GitHub Pages
        id: deploy
        uses: actions/deploy-pages@v4
```
## Workflow Steps Explained

### 1. Trigger
* The workflow is triggered on pushes to the `main` branch.

### 2. Build Job (`build-deploy`)
* Runs on: `ubuntu-latest`.
* Steps:
  1. **Checkout repository**: Uses the `actions/checkout` action to clone the repository
  2. **Set up R**: Uses the `r-lib/actions/setup-r` action to install R.
  3. **Install system dependencies**: Installs Quarto and the `leaflet` (for interactive maps) package.
  4. **Set up Quarto**: Uses the `quarto-dev/quarto-actions/setup` action to install Quarto.
  5. **Render Quarto Site**: Changes directory to `quarto_docs` and executes:
      ```
      Rscript -e "quarto::quarto_render()"
      ```
  6. **Upload site artifact**: Uses the `actions/upload-pages-artifact` action to upload the rendered site located in `quarto_docs/_site`.

### 3. Deploy Job (`deploy`)
* Depends on the successful completion of the `build-deploy` job.
* Runs on: `ubuntu-latest`.
* Permissions: 
  * `pages: write` -> allows deploying to GitHub Pages
  * `id-token: write` -> allows authentication for deployment.
* Steps:
  1. **Deploy to GitHub Pages**: Uses the `actions/deploy-pages` action to deploy the uploaded site artifact to GitHub Pages.

### Notes
* All static pages are served from GitHub Pages.
* No Docker image is required; the workflow runs in the GitHub Actions environment `ubuntu-latest`.
* Deep links are preserved by Quarto during the rendering process.


# Reference GitHub Pages

Learn more about GitHub Pages at https://pages.github.com, the official
documentation https://docs.github.com/en/pages