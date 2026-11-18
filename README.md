# RSS.CHAT REPLY MANAGER FOR JEKYLL

## Setup

1. Clone the repo
1. Rename config.py.example to config.py
1. Create any necessary tokens
1. Populate config.py with our username feed
1. Copy [`examples/fetch_replies.yml`](examples/fetch_replies.yml) into your site's `.github/workflows/` and fill in its placeholders: your commit back to the repo step and your deploy step.
1. Update your post.html template to include the code below and style accordingly.

## Usage

```bash
python script.py
```

## Changes and Release

Increment the package.json version, using SEMVER when commiting new changes to the script.
The GitHub action will  create a new release using the last commit message as the release note.

## Notes

### Example reply format

Each reply is a yaml block within a slug.yml file in the `_data/replies` folder. This exposes the data to the Liquid templating and can be displayed in a post.html file as show below

```yaml
- guid: https://demo.rss.chat/?id=0000
  author: Bob
  author_url: https://demo.rss.chat/users/bob/rss.xml
  pub_date: Fri, 24 Jul 2026 16:19:39 GMT
  html: I like that this is a reply on your site as well as <a href="https://rss.chat</a>.
  markdown: I like that this is a reply on your site as well as rss.chat.
  in_reply_to: https://demo.rss.chat/?id=0000
```

### Example template.html file

Add this to your post.html template to pull in the replies for each post (matching on slug)

```html
{% raw %}
{%- assign url_parts = page.url | split: "/" | where_exp: "part", "part != ''" -%}
{%- assign reply_slug = url_parts | join: "-" -%}
{%- assign replies = site.data.replies[reply_slug] -%}

{%- if replies and replies.size > 0 -%}
<div class="replies">
    <ol class="list">
        {%- for reply in replies -%}
        <li class="item">
            <article class="no-photo">
                <div class="author">
                    {%- if reply.author_url -%}
                    <a href="{{ reply.author_url | escape }}">{{ reply.author | escape }}</a>
                    {%- else -%}
                    {{ reply.author | escape }}
                    {%- endif -%}
                </div>
                <div class="content">{{ reply.html }}</div>
                <div class="meta">
                    <time
                        datetime="{{ reply.pub_date | date_to_xmlschema }}">{{ reply.pub_date | date: "%-d %B %Y" }}</time>
                    {% if reply.guid %} | <a href="{{ reply.guid | escape }}">Permalink</a>{%- endif -%}
                </div>
            </article>
        </li>
        {%- endfor -%}
    </ol>
</div>
{%- endif -%}
{% endraw %}
```
