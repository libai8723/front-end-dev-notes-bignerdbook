A Very Chatty Blog of LiBai
===

## Intro ##
This is a personal log about learning front end development, mainly about the book <Front-End Web Development The Big Nerd Ranch Guide>.

and i think the Github Pages is perfect for hosting such a personal log. On 2019-09-21, I think I shoud put other blog here. For example, vue, react, maven, java Spring boot.


## Useful link ##
[Mastering Markdown](https://guides.github.com/features/mastering-markdown/)

## Post Entries ##
<ol>
  {% for post in site.posts %}
    <li>
      <a href="/front-end-dev-notes-bignerdbook{{ post.url }}">{{ post.title }} at {{ post.date }}</a>
    </li>
  {% endfor %}
</ol>
