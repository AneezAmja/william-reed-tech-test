# william-reed-tech-test

This project is a simple news article page built with HTML, CSS (Bootstrap), and JavaScript. It dynamically fetches and displays a news article, including its related and popular news, as well as a comments section from a local JSON file.

## Table of Contents

- [Features](#features)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
- [Explantion](#explantion)
- [Potential features](#potential-features)
- [Contributing](#contributing)

## Features

- Displays a main news article with an image, headline, author, and date.
- Dynamically populates the article content from a JSON file.
- Includes a comments section with nested replies.
- Allows users to submit new comments.

## Getting Started

### Prerequisites

Ensure you have a modern web browser installed. No additional software is required.

### Installation
    1. Clone this repository:
    ```
    git clone https://github.com/yourusername/single-post-news-article.git
    ```

    2. Navigate to the project directory:
        ```bash cd single-post-news-article ```

    3. Open index.html in your preferred web browser.

## Explantion of solution
The first thing I did when I opened up the testURL was to check the console, as most errors will be flagged here. When opening the console, there was an error labeled "Uncaught ReferenceError: mobileCheck is not defined" this lead me to believe that mobileCheck hasn't been defined anywhere within the project. At further inspection of the codebase I noticed immediately that the script tags where incorrectly placed, I moved them towards the end of the body tag ensuring that the bootstrap js file was loaded before the custom scripts. 


Then I went to find where the error was occuring, it was due to the following code: 

```
// Journey Data Population
  DataLayer.journey_data.is_mobile = mobileCheck();
// Is mobile
  window.mobileCheck = function() {
    // rest of function code...
  };

```

This proved that my though process was correct and that mobileCheck is being called before it is defined. I noticed that it is also made to be part of the window Object ```window.mobileCheck = function(){}```. I opted to change it to the following:

```
      function mobileCheck() {
        // rest of function code...
      }

      // Journey Data Population
      DataLayer.journey_data.is_mobile = mobileCheck();
```

I first changed the invocation order, so the function is defined first then it is being declared - this fixed the console error. 

Nitpick: I changed the function to a Descriptive Function Name rather than a function assigned to the window Object. This is purely due to the fact that this application is pertaining to one file, so there is not a need to have this function be global throughout the project. If this application grows, it may be beneficial to revert this particular change back.


The next issue was to make the Related News and World News articles dynamic as currently they were staticly generated. In order to maintain the coding convention, initally I implemented this in a similar manner as the existing code for the comments or body. Which can be seen here:

```
           //Related news
            const relatedNewsContainer =
              document.querySelector(".related-news");

            data.relatedNews.forEach((relatedNewsItem) => {
              const newsItem = document.createElement("li");
              newsItem.className = "list-group-item";

              relatedNewsContainer.appendChild(newsItem);

              const newsLink = document.createElement("a");
              newsLink.classList.add("text-decoration-none");
              newsLink.href = relatedNewsItem.link;

              newsItem.appendChild(newsLink);

              const newsTitle = document.createElement("h5");
              newsTitle.textContent = relatedNewsItem.title;

              newsLink.appendChild(newsTitle);

              const newsDescription = document.createElement("p");
              newsDescription.textContent = relatedNewsItem.description;

              newsLink.appendChild(newsDescription);
            });

            //Popular news
            const popularNewsContainer =
              document.querySelector(".popular-news");

            data.popularNews.forEach((popularNewsItem) => {
              const newsItem = document.createElement("li");
              newsItem.className = "list-group-item";

              popularNewsContainer.appendChild(newsItem);

              const newsLink = document.createElement("a");
              newsLink.classList.add("text-decoration-none");
              newsLink.href = popularNewsItem.link;

              newsItem.appendChild(newsLink);

              const newsTitle = document.createElement("h5");
              newsTitle.textContent = popularNewsItem.title;

              newsLink.appendChild(newsTitle);

              const newsDescription = document.createElement("p");
              newsDescription.textContent = popularNewsItem.description;

              newsLink.appendChild(newsDescription);
            });
```

However, I was not happy with this initial implementation, if the requirements of the task changed and that there should be a featured image within the news article panel then I would have to update the codebase with it in 2 places (related news, world news). This raised concerns about maintainability and efficiency. 

I wanted to adhere to the Don't Repeat Yourself (DRY) principles, thus I created a function that would seamlessly generate me these news article. What's fantastic about this implementation is that it simplifies futre additions. For example, incorporating a "South American News" category would require minimal effort. As long as the dataset is updated, the implementation of this is one line of code as opposed to the previous implementation. This approach significantly reduces code complexity and ensures consistency across different news sections.


This is the final implementation I went with:

```
    function populateNews(newsData, selectedContainer) {
        const newsContainer = document.querySelector(selectedContainer);

        newsData.forEach((newsItem) => {
          const item = document.createElement("li");
          item.className = "list-group-item";
          item.setAttribute("role", "listitem");
          newsContainer.append(item);

          const articleLink = document.createElement("a");
          articleLink.classList.add("text-decoration-none");
          articleLink.href = newsItem.link;

          item.appendChild(articleLink);

          const articleTitle = document.createElement("h5");
          articleTitle.textContent = newsItem.title;

          articleLink.appendChild(articleTitle);

          const articleDescription = document.createElement("p");
          articleDescription.textContent = newsItem.description;

          articleLink.appendChild(articleDescription);
        });
      }
```

```
      // Populating the news article panel with data
            populateNews(data.relatedNews, ".related-news");
            populateNews(data.popularNews, ".popular-news");
```

## Potential features
In a production environment I would have liked to utilised multiple things: 

* Utilising [Browserstack](https://www.browserstack.com/) which is a mobile testing platform where you can test your websites through an emulation device. This provides a more accurate view of the `mobileCheck` function's behavior compared to Chrome's emulation tools. As the function checks the userAgent which cannot be done through Chrome's emulation tools.
* I would also have liked to Implement unit, functional, integration, and end-to-end (E2E) tests for each individual functions. This comprehensive approach minimises the risk of errors in production.


## Contributing

Contributions are very welcome. Please follow the below steps:

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request
