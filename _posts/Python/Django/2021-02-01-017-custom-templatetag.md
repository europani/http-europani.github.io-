---
layout: post
title: 커스텀 템플릿태그(templatetags)
categories: Django
tags: [Django]
---

템플릿에서 다양한 기능을 수행하는 커스텀태그를 만들 수 있음

project명/app명/**templatetags** 디렉토리에 모듈파일 생성

 -> templatetags 디렉토리명은 절대 바뀌면 안됨. 

 -> 모듈명은 마음대로 생성가능하나 다른 앱의 모듈과도 이름이 같아서는 안된다. (위치와 상관없이 모듈명으로 로드되기 때문에 충돌발생)

○ 디렉토리 경로

```
project/
     apps/
    	templatetags/
            custom_tag.py
    	__init__.py
        models.py
        view.py
```

● 커스텀태그 모듈 (custom\_tag.py)

1\. filter

 - template에서 사용할 필터 정의

```python
from django import template 
register = template.Library()

@register.filter
def lower(value):
    return value.lower()
```

```template
{% raw %}
{% load custom_tag %}

<div>{{ variable|lower }}</div>
{% endraw %}
```

2\. simple\_tag

 - 단순히 특정값을 출력함

```python
from django import template 
register = template.Library()

@register.simple_tag
def current_time():
  return datetime.now().strftime("%Y-%m-%d %H:%M")
```

```template
{% raw %}
{% load custom_tag %}

<div>The time is {{ current_time }}</div>
{% endraw %}
```

3\. inclusion\_tag

 - 다른 템플릿을 포함시킴

```python
from django import template 
register = template.Library()

@register.inclusion_tag('link.html', takes_context=True)
def jump_link(context):
    return {
        'link' : context['home_link'],
        'title' : context['home_title'],
    }
```

→ tag를 사용하는 템플릿(index.html)의 context의 값을 가져와 호출하는 템플릿(results.html)으로 전달가능

```template
{% raw %}
# index.html
{% load custom_tag %}

<div>{{ jump_link }}</div>


# link.html
Jump directly to <a href="{{ link }}">{{ title }}</a>.

{% endraw %}
```

● template.html

```template
{% raw %}
{% load custom_tag %}       // {% load 모듈명 %} 으로 템플릿에서 호출
{% endraw %}
```

