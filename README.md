# skelettes
Templates Craft 3




Using Wordsmith words trimmer filter:


|chop(limit=25, unit='w', allowedTags='<em>, <p>, <b>')




Creating an array:
---

{# Create an entry query with the 'section' and 'limit' parameters #}
{% set myEntryQuery = craft.entries()
    .section('blog')
    .limit(10) %}

{# Fetch the entries #}
{% set entries = myEntryQuery.all() %}

{# Display the entries #}
{% for entry in entries %}
    <article>
        <h1><a href="{{ entry.url }}">{{ entry.title }}</a></h1>
        {{ entry.summary }}
        <a href="{{ entry.url }}">Continue reading</a>
    </article>
{% endfor %}



Creating a single:
---

{# Create an entry query with the 'section' and 'limit' parameters #}
{% set myEntryQuery = craft.entries()
    .section('pages')
    .one() %}

{# Display the entries #}
{% for entry in myEntryQuery %}
    <article>
        <h1><a href="{{ entry.url }}">{{ entry.title }}</a></h1>
        {{ entry.summary }}
        <a href="{{ entry.url }}">Continue reading</a>
    </article>
{% endfor %}








Craft Filters:
 <pre>

	{{ some_html|striptags }}

	<p>The striptags filter strips SGML/XML (HTML) tags and replace adjacent whitespace by one space </p>

 </pre>

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
</pre>


<h2> 2. Conditionals </h2>

<pre>
    {% if something == 'value' %}
        ...
    {% elseif somethingElse == 'otherValue' %}
        ...
    {% else %}
        ...
    {% endif %}
</pre>

<pre>
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


<h2>Date formats</h2>
<pre>              

F	A full textual representation of a month, such as January or March


{{ entry.postDate | date }}, {{ entry.postDate |date('medium') }}
results: 29 Nov. 2018

{{ entry.postDate |date('short') }}
results: 29/11/2018

{{ entry.postDate |datetime('long', locale='fr-FR', timezone='UTC') }}
29 NOVEMBRE 2018

{{ entry.postDate |date('full') }}
results: Wednesday, September 26, 2018

{{ entry.postDate |date('c') }}
results: 2018-11-29T06:30:00



1/

{{ entry.postDate | date("d, F d") }}
results: Friday, December 22

2/
{{ entry.postDate | date("d M, Y") }}
results: 17 January, 2018

3/
{{ entry.postDate | date("d F, Y") }}
results: 17 January, 2018

4/
{{ entry.postDate | date('M j, Y') }}
results: 18-07-2011

{{ entry.postDate | date('d-m-Y') }}
results: 18-07-2011


</pre>



<h2>Is it possible to change a loop if mobile browser is detected?</h2>

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

<pre>
    {% set existingIds = [] %}

    {% for event in events %}
        {% if event.id not in existingIds %}
            {{ event.title }}

            {% set existingIds = existingIds|merge([event.id]) %}
        {% endif %}
    {% endfor %}
</pre>
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

<pre>
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

</pre>
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

<h2>Pagination</h2>

<blockquote>
<div id="pagination">

	<ul class="pagination pagination-lg">

		{% if pageInfo.prevUrl %}
			<li class="page"><a href="{{ pageInfo.prevUrl }}">&#171; Prev</a></li>
		{% endif %}

		{% if pageInfo.currentPage - 4 > 1 %}
			<li class="page gap"><span>...</span></li>
		{% endif %}

		{% for page, url in pageInfo.getPrevUrls(4) %}
			<li class="page"><a href="{{ url }}">{{ page }}</a></li>
		{% endfor %}

			<li class="page current active on"><span>{{ pageInfo.currentPage }}</span></li>

		{% for page, url in pageInfo.getNextUrls(4) %}
			<li class="page"><a href="{{ url }}">{{ page }}</a></li>
		{% endfor %}

		{% if pageInfo.totalPages - pageInfo.currentPage > 4 %}
			<li class="page gap"><span>...</span></li>
			<a href="{{ pageInfo.lastUrl }}">{{pageInfo.totalPages}}</a>
		{% endif %}


		{% if pageInfo.nextUrl %}
			<li class="page"><a href="{{ pageInfo.nextUrl }}">Next &#187;</a></li>
		{% endif %}

	</ul>

</div>
</blockquote>

