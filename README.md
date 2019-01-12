# skelettes
Templates Craft 3


Craft Filters:


{{ some_html|striptags }}

<p>The striptags filter strips SGML/XML (HTML) tags and replace adjacent whitespace by one space </p>


<h2>Author Tags</h2>

<pre>

{{ entry.author.socialTwitter }}
{{ entry.author.socialFacebook }}
{{ entry.author.fullName }}
{{ entry.author.bio }}

</pre>



<h2>Entries except the first one</h2>
<pre>
{% set entries = craft.entries.section('articles').limit(3).find %}
{% set firstEntry = entries[0] %}
{% set remainingEntries = entries|without(firstEntry) %}

<h2> 2. Conditionals </h2>

{% if something == 'value' %}
    ...
{% elseif somethingElse == 'otherValue' %}
    ...
{% else %}
    ...
{% endif %}


{% for block in contactDetails.myMatrixField %}
     {{ block.location }}
     {{ block.address }}
{% endfor %}
</pre>

<h2> Sharring </h2>
<pre>
{% for block in socialNetworks.socialLinks %}
  {% if block.facebook %}     {{ block.facebook }}  {% endif %}
  {% if block.twitter %}      {{ block.twitter }}   {% endif %}
  {% if block.youtube %}      {{ block.youtube }}   {% endif %}
  {% if block.instagram %}    {{ block.instagram }} {% endif %}
{% endfor %}
</pre>



<pre>              
F	A full textual representation of a month, such as January or March

Friday, December 22
{{ entry.postDate | date("d, F d") }}



{{ entry.postDate | date("d M, Y") }}

17 January 2018:
{{ entry.postDate | date("d F, Y") }}


</pre>


<h2>Is it possible to change a loop if mobile browser is detected?</h2>
<p>http://craftcms.stackexchange.com/questions/11250/is-it-possible-to-change-a-loop-if-mobile-browser-is-detected
    </p>
    <pre>
    {% if craft.request.isMobileBrowser %}
        {% for batch in craft.entries.section('afval')|batch(2) %}
            ...
        {% endfor %}
    {% else %}
        {% for batch in craft.entries.section('afval')|batch(3) %}
            ...
        {% endfor %}
    {% endif %}
    </pre>
    Or 
    <pre>
    {% set batch = craft.request.isMobileBrowser ? 2 : 3 %}

    {% for batch in craft.entries.section('afval') | batch(batch) %}
        ...
    {% endfor %}
    </pre>

<h2>Check and remove repeat entries from loop</h2>

    {% set existingIds = [] %}

    {% for event in events %}
        {% if event.id not in existingIds %}
            {{ event.title }}

            {% set existingIds = existingIds|merge([event.id]) %}
        {% endif %}
    {% endfor %}

    <h2>Slide Count</h2>

    <pre>
    {% set slides = craft.entries.section('events').featured('1').limit(null) %}

    {% set count = 0 %}
    {% for slide in slides %}
        {% if count < 5 and slide.dateTime|date_modify('tomorrow 5am')|date('U') < now|date('U') %}
            {{ slide.dateTime }}
            {% set count = count + 1 %}
        {% endif %}
    {% endfor %}
    </pre>


<h2>LOOPS</h2>

<h2>Pagination Loop</h2>

    {% paginate craft.entries.section('news').limit(10) as pageInfo, newsEntries %}

    {% for entry in newsEntries %}

            {% set image = entry.featuredImage.one() %}
            {% set entryCategory = entry.categories.one() %}
            {% set transform= {
                                mode: 'crop',
                                width: 675,
                                height: 339,
                                quality: 80
                                } 
            %}
            {% do image.setTransform(transform) %}

            {% include '_snippets/entryCard.html' %}                         

    {% endfor %}

    {% include '_snippets/pagination.html' %}


<h2>Pagination with Eagerload </h2>

    {% set queryParams = {

        with: [
            'gridMatrixBlock1:rowItem1',
            ['gridMatrixBlock1:rowItem1.gridimageFile', {
                kind: 'image',
                withTransforms: projectThumb
            }],

        ]
    } %}
    {% set allEntries = craft.entries(queryParams).limit(itemsPerPage) %}

    or

    {% paginate craft.entries(queryParams).limit(10) as pageInfo, newsEntries %}



<h2>Loop With Eager Load </h2>

    {% set recentEntries = craft.entries()
        .section('news')
        .with([ 
           'featuredImage',
           'relatedCategories'
              ])
        .limit(4) 
    %}

    {% set entries = recentEntries.all() %}

    {% for entry in entries %}

        {% set image = entry.featuredImage[0] ?? null %}
        {% set transform = {
            mode: 'crop',
            width: 150,
            height: 90,
            quality: 75,
            position: 'center-center'
        } %}
        {% do image.setTransform(transform) %}


        {% include '_snippets/entryCard.html' %} 
         
     {% endfor %}


<h2>Contributors</h2>

<blockquote>
    
    {% if entry.contributors | length %}
    
        <div class="contributors-images">

            {% for authors in entry.contributors %}
                {% set authorImage = authors.contributorPhoto.first() %}
                    {% if authorImage | length %}     
                        <a href="{{ authors.url }}">
                           <img alt="" src="{{ authorImage.url }}" class="avatar avatar-234 photo">
                        </a>
                    {% else %}     
                        <img alt="" src="/img/profile-default.png" class="avatar avatar-234 photo">      
                    {% endif %}
            {% endfor %}

        </div>
        <div class="contributors-info"> 

            <span class="by">by </span>
            {% for authors in entry.contributors %}
                <span itemprop="author" class="author">
                    <a href="{{ authors.url }}">{{ authors.title }}</a> 
                </span> {%- if not loop.last -%}, and {% endif %}
            {% endfor %}

        </div>
   
    {% endif %}

</blockquote>

<h2>List of categories with related articles </h2>

<blockquote>
    
    {% for category in craft.categories.group('categoryGroup') %}

        <a href="{{ category.url }}">{{ category.title }}</a>

        {% for post in craft.entries.section('posts').relatedTo(category) %}
        {# your markup #}
        {% endfor %}

    {% endfor %}

</blockquote>

<h2>Creating Social Share Links </h2>




