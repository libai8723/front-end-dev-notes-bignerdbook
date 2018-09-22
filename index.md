front-end-dev-notes-bignerdbook
===

## Intro ##
This is a personal log about learning front end development, mainly about the book <Front-End Web Development The Big Nerd Ranch Guide>.

and i think the Github Pages is perfect for hosting such a personal log.

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
