# My Web Scraping projects

* scraping bascis
    - A simple web scraping project to review the scrapy framework
---

- Project 1: Scraping quotes from the [quotes website]('https://quotes.toscrape.com')

    - A better abstraction by ommitting start_requests method.

    ```python
    # normal design
    class NameofSpider:
        name="given_name"

        def start_requests(self):
            urls = [
                "url_1",
                "url_2"
            ]
            for url in urls:
                yield scrapy.Request(url=url, callback=self.parse)
        
        def parse(self, response):
            pass
    
    # better design
    class NameofSpider:
        name = "given_name"
        start_urls = [
            "url_1",
            "url_2",
        ]

        def parse(self, response):
            pass
    ```

    - using jsonl over json

    ```sh
    # use this line to extract data into json.
    # json file will be overwritten, once a file with the same name is written
        scrapy crawl <spider_name> -O <filename>.json
    
    # use this line to extract data into jsonl.
    # jsonl gives you the ability to append to the existing file.
        scrapy crawl <spider_name> -o <filename>.json
    ```

    - Follow links in a give url, instead typing of all links: you need the ability to extract href in tags

    ```html
        <ul class="pager">
            <li class="next">
                <a href="/page/2/">Next <span aria-hidden="true">&rarr;<span></a>
            </li>
        </ul>
    ```
    ```python
        # The next_page extraction depends on the html structure
        next_page = response.css('li.next a::attr(href)').get() => "/page/2/"
        if next_page is not None:
            # response.follow() take relative url as argument.
            yield response.follow(next_page, callback=self.parse)
        # response.follow() also takes tag
        for a in response.css("ul.pager a"):
            yield  response.follow(a_element, callback=self.parse)
        # response.follow() can also iterate over a list of element with
        # combined with yield.
        anchors = response.css("ul.pager a")
        yield response.follow(anchors, callback=self.parse)
    ```