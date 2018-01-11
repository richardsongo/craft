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




