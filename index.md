front-end-dev-notes-bignerdbook
===

## Intro ##
This is a personal log about learning front end development, mainly about the book <Front-End Web Development The Big Nerd Ranch Guide>.

and i think the Github Pages is perfect for hosting such a personal log.

## Useful link ##
[Mastering Markdown](https://guides.github.com/features/mastering-markdown/)

## Posts ##
<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>
