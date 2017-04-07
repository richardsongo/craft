# skelettes
Templates


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

{% set entries = craft.entries.section('articles').limit(3).find %}
{% set firstEntry = entries[0] %}
{% set remainingEntries = entries|without(firstEntry) %}

<h2> 2. Conditionals </h2>
<pre>
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

{% for block in socialNetworks.socialLinks %}
{% if block.facebook %}     {{ block.facebook }}  {% endif %}
{% if block.twitter %}      {{ block.twitter }}   {% endif %}
{% if block.youtube %}      {{ block.youtube }}   {% endif %}
{% if block.instagram %}    {{ block.instagram }} {% endif %}
{% endfor %}
</pre>

<pre>
<div class="highlight highlight-text-html-django">

<ul>
<li id="facebook"><a href="#" target="_blank" alt="Facebook" title="Facebook">  <i class="fa fa-facebook"></i>   </a></li>
<li id="twitter"><a href="#" target="_blank" alt="Twitter" title="Twitter">          <i class="fa fa-twitter"></i>    </a></li>
<li id="youtube"><a href="#" target="_blank" alt="YouTube" title="YouTube"> <i class="fa fa-youtube"></i>    </a></li>
<li id="itunes"><a href="#" target="_blank" alt="iTunes" title="iTunes"> <i class="fa fa-instagram"></i> </a></li>
</ul>              
</div>
</pre>

  <pre>              
F	A full textual representation of a month, such as January or March
{{ entry.postDate | date("d M, Y") }}
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



<pre>
<article class="col-md-4 col-sm-6 col-xs-12 newsitem space-lg-5">
	<div class="newsitem-inner">

		<a href="{{ entry.url }}">

				<figure>
					<img src="{{ image.url }}" alt="{{ image.title }}" width="{{ image.width }}" height="{{ image.height }}">
				</figure>

				<h2 itemprop="headline" class="h3">{{ column.title }}</h2>
				<span class="date text-copy">{% if entryCategory %} {{ entryCategory }} / {% endif %} {{ column.postDate|date('j F, Y') }} </span>

			{% if column.summary |length %}

		        <p class="post-summary story-summary hidden-xs">
		        	{{ column.summary|hacksaw(hack='c', limit='150') }}
		        	<span class="arrow-right">Read more</span>
		        </p>

			{% else %}

		        <p class="post-summary story-summary hidden-xs">
		        	{{ column.body|hacksaw(hack='c', limit='150') }}
					<span class="arrow-right">Read more</span>
				</p>

			{% endif %}
			
		</a>

	</div>
</article>
</pre>
