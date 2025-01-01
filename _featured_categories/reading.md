---
# Featured tags need to have either the `list` or `grid` layout (PRO only).
# 여기서는 list 레이아웃을 사용한다고 지정되어 있음
# 이 설정은 해당 태그 페이지에서 list 형태로 포스트들이 나열된 페이지가 생성되도록 함
# slug: example은 example 태그에 해당하는 페이지가 /example/라는 URL 경로로 생성될 것임을 의미

layout: list

# The title of the tag's page.
title: Reading Paper

# The name of the tag, used in a post's front matter (e.g. tags: [<slug>]).
slug: reading

# (Optional) Write a short (~150 characters) description of this featured tag.
description: >
  This is a featured category, which have their own page.
  Check out `_featured_tags/example.md` to learn how to create your own.

# (Optional) You can disable grouping posts by date.
# no_groups: true

# Exclude this example category from the sitemap.
# DON'T USE THIS SETTING IN YOUR CATEGORIES!
sitemap: false
---