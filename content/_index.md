---
# Leave the homepage title empty to use the site title
title: ''
date: 2022-10-24
type: landing

sections:
  - block: hero
    demo: true # Only display this section in the Hugo Blox Builder demo site
    content:
      title: Hugo Academic Theme
      image:
        filename: hero-academic.png
      cta:
        label: '**Get Started**'
        url: https://hugoblox.com/templates/
      cta_alt:
        label: Ask a question
        url: https://discord.gg/z8wNYzb
      cta_note:
        label: >-
          <div style="text-shadow: none;"><a class="github-button" href="https://github.com/HugoBlox/hugo-blox-builder" data-icon="octicon-star" data-size="large" data-show-count="true" aria-label="Star">Star Hugo Blox Builder</a></div><div style="text-shadow: none;"><a class="github-button" href="https://github.com/HugoBlox/theme-academic-cv" data-icon="octicon-star" data-size="large" data-show-count="true" aria-label="Star">Star the Academic template</a></div>
      text: |-
        **Generated by Hugo Blox Builder - the FREE, Hugo-based open source website builder trusted by 500,000+ sites.**

        **Easily build anything with blocks - no-code required!**

        From landing pages, second brains, and courses to academic resumés, conferences, and tech blogs.

        <!--Custom spacing-->
        <div class="mb-3"></div>
        <!--GitHub Button JS-->
        <script async defer src="https://buttons.github.io/buttons.js"></script>
    design:
      background:
        gradient_end: '#FF1842'
        gradient_start: '#FF2950'
        text_color_light: true
  - block: about.biography
    id: about
    content:
      title: ''
      # Choose a user profile to display (a folder name within `content/authors/`)
      username: admin
  # - block: skills
  #   content:
  #     title: Skills
  #     text: ''
  #     # Choose a user to display skills from (a folder name within `content/authors/`)
  #     username: admin
  #   design:
  #     columns: '1'
  - block: experience
    content:
      title: Experience
      # Date format for experience
      #   Refer to https://docs.hugoblox.com/customization/#date-format
      date_format: Jan 2006
      # Experiences.
      #   Add/remove as many `experience` items below as you like.
      #   Required fields are `title`, `company`, and `date_start`.
      #   Leave `date_end` empty if it's your current employer.
      #   Begin multi-line descriptions with YAML's `|2-` multi-line prefix.
      items:
        - title: Assistant Sport Scientist
          company: The Ohio State University Department of Athletics and Human Performance Collaborative
          company_url: 'https://hpc.osu.edu'
          company_logo: ''
          location: Columbus, OH
          date_start: '2022-08-01'
          date_end: ''
          description: ''
        - title: X-Force Fellow in Data Science and Engineering Research
          company: National Security Innovation Network and Army National Guard
          company_url: ''
          company_logo: ''
          location: Remote
          date_start: '2023-06-01'
          date_end: '2023-08-08'
          description: ''
        - title: Data Analytics Intern
          company: Orlando Soccer Club
          company_url: ''
          company_logo: ''
          location: Remote
          date_start: '2023-01-01'
          date_end: '2023-06-01'
          description: ''
        - title: Undergraduate Research Intern
          company: The Ohio State University MOvES Lab
          company_url: ''
          company_logo: ''
          location: Columbus, OH
          date_start: '2021-01-31'
          date_end: '2022-12-01'
          description: ''
        - title: Fellow and Data Science Intern
          company: Women in Sports Tech and Perch
          company_url: ''
          company_logo: ''
          location: Remote
          date_start: '2022-06-01'
          date_end: '2022-08-01'
          description: ''
        - title: Undergraduate Strength and Conditioning Intern
          company: The Ohio State University Department of Athletics
          company_url: ''
          company_logo: ''
          location: Columbus, OH
          date_start: '2021-06-01'
          date_end: '2021-08-01'
          description: ''
    design:
      columns: '2'
  - block: accomplishments
    content:
      # Note: `&shy;` is used to add a 'soft' hyphen in a long heading.
      title: 'Certifications'
      subtitle:
      # Date format: https://docs.hugoblox.com/customization/#date-format
      date_format: Jan 2006
      # Accomplishments.
      #   Add/remove as many `item` blocks below as you like.
      #   `title`, `organization`, and `date_start` are the required parameters.
      #   Leave other parameters empty if not required.
      #   Begin multi-line descriptions with YAML's `|2-` multi-line prefix.
      items:
        - certificate_url: 'https://learn.microsoft.com/en-us/credentials/certifications/azure-data-fundamentals/'
          date_end: ''
          date_start: '2023-09-01'
          description: ''
          # icon: ''
          organization: Microsoft
          organization_url: 'https://learn.microsoft.com/'
          title: 'Microsoft Certified: Azure Data Fundamentals'
          url: ''
        - certificate_url: 'https://www.coursera.org/specializations/data-science-foundations-r'
          date_start: '2023-05-31'
          organization: 'Coursera, Johns Hopkins University'
          title: 'Data Science: Foundations using R Specialization'
        # - certificate_url: 'https://www.coursera.org/specializations/data-science-foundations-r'
        #   date_end: ''
        #   date_start: '2023-05-31'
        #   description: ''
        #   # icon: ''
        #   organization: Coursera
        #   organization_url: https://www.coursera.org
        #   title: 'Data Science: Foundations using R Specialization'
        #   url: ''
        - certificate_url: 'https://www.nsca.com/certification/cscs/'
          date_end: ''
          date_start: '2022-05-31'
          description: ''
          # icon: ''
          organization: National Strength and Conditioning Association
          organization_url: 'https://www.nsca.com/'
          title: 'Certified Strength and Conditioning Specialist'
          url: ''
    design:
      columns: '2'
  - block: collection
    id: posts
    content:
      title: Posts and Projects
      subtitle: ''
      text: |-
        {{% callout note %}}
        [Click here to view all and filter posts](./post/).
        {{% /callout %}}
      # Choose how many pages you would like to display (0 = all pages)
      count: 5
      # Filter on criteria
      filters:
        folders:
          - post
        author: ""
        category: ""
        tag: ""
        exclude_featured: false
        exclude_future: false
        exclude_past: false
        publication_type: ""
      # Choose how many pages you would like to offset by
      offset: 0
      # Page order: descending (desc) or ascending (asc) date.
      order: desc
    design:
      # Choose a layout view
      view: compact
      columns: '2'
  # - block: portfolio
  #   id: projects
  #   content:
  #     title: Projects
  #     filters:
  #       folders:
  #         - project
  #     # Default filter index (e.g. 0 corresponds to the first `filter_button` instance below).
  #     default_button_index: 0
  #     # Filter toolbar (optional).
  #     # Add or remove as many filters (`filter_button` instances) as you like.
  #     # To show all items, set `tag` to "*".
  #     # To filter by a specific tag, set `tag` to an existing tag name.
  #     # To remove the toolbar, delete the entire `filter_button` block.
  #     buttons:
  #       - name: All
  #         tag: '*'
  #       - name: Deep Learning
  #         tag: Deep Learning
  #       - name: Other
  #         tag: Demo
  #   design:
  #     # Choose how many columns the section has. Valid values: '1' or '2'.
  #     columns: '1'
  #     view: showcase
  #     # For Showcase view, flip alternate rows?
  #     flip_alt_rows: false
  # - block: markdown
  #   content:
  #     title: Gallery
  #     subtitle: ''
  #     text: |-
  #       {{< gallery album="demo" >}}
  #   design:
  #     columns: '1'
  # - block: collection
  #   id: featured
  #   content:
  #     title: Featured Publications
  #     filters:
  #       folders:
  #         - publication
  #       featured_only: true
  #   design:
  #     columns: '2'
  #     view: card
  - block: collection
    id: publications
    count: 0
    content:
      title: Publications
      text: |-
        {{% callout note %}}
        [Click here to view all and filter publications](./publication/).
        {{% /callout %}}
      filters:
        folders:
          - publication
        author: ""
        category: ""
        tag: ""
        exclude_featured: false
        exclude_future: false
        exclude_past: false
        publication_type: "article-journal"
    design:
      columns: '2'
      view: citation
  - block: collection
    id: talks
    count: 5
    content:
      title: Presentations
      filters:
        folders:
          - event
    design:
      columns: '2'
      view: compact
  # - block: tag_cloud
  #   content:
  #     title: Popular Topics
  #   design:
  #     columns: '2'
  - block: contact
    id: contact
    content:
      title: Contact
      subtitle:
      text: |-
        Feel free to reach out to chat about sports or sport science or anything really!! I'd love to connect!!!
      # Contact (add or remove contact options as necessary)
      email: vatne.1@buckeyemail.osu.edu
      address:
        street: Schumaker Complex, 615 Irving Schottenstein Dr
        city: Columbus
        region: OH
        postcode: '43210'
        country: United States
        country_code: US
      # directions: Sport science offices inside the athletic training room
      # office_hours:
      #   - 'Tuesday 9:00 to 15:00'
      #   - 'Wednesday 09:00 to 15:00'
      # Choose a map provider in `params.yaml` to show a map from these coordinates
      contact_links:
        - icon: twitter
          icon_pack: fab
          name: DM Me!
          link: 'https://twitter.com/emaly_vatne'
      # Automatically link email and phone or display as text?
      autolink: true
    design:
      columns: '2'
---
