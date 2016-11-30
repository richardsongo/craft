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


F	A full textual representation of a month, such as January or March
{{ entry.postDate | date("d M, Y") }}

