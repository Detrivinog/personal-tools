---
title: {{title}}
authors: {{authors}}
year: {{date | format("YYYY")}}
keywords: [{{allTags}}]
citekey: {{citekey}}
---

Title:: {{title}}
Bibliography:: {{bibliography}}
url:: {{url}}
Zotero link:: {{pdfZoteroLink}}

## Reading notes
{% persist "annotations" %}

{%-
    set zoteroColors = {
	    "#2ea8e5": "blue",
        "#5fb236": "green",
        "#a28ae5": "purple",
        "#ff6666": "red",
        "#ffd400": "yellow",
        "#f19837": "orange",
        "#aaaaaa": "grey",
        "#e56eee": "magenta"
    }
-%}

{%-
   set colorHeading = {
		"blue": "Study in more detail",
		"green": "Definitions",
		"purple": "References",
		"red": "Title #1",
		"yellow": "Highlight",
		"orange": "Questions, goals, problems",
		"grey": "Code, formulas, equations",
		"magenta": "Title #2"
   }
-%}

{%- macro calloutHeader(type) -%}
    {%- switch type -%}
        {%- case "highlight" -%}
        
        {%- case "image" -%}
        
        {%- default -%}
        Note
    {%- endswitch -%}
{%- endmacro %}

{%- set newAnnot = [] -%}
{%- set newAnnotations = [] -%}
{%- set annotations = annotations | filterby("date", "dateafter", lastImportDate) %}

{% if annotations.length > 0 %}
*Imported: {{importDate | format("YYYY-MM-DD HH:mm")}}*

{%- for annot in annotations -%}

    {%- if annot.color in zoteroColors -%}
        {%- set customColor = zoteroColors[annot.color] -%}
    {%- elif annot.colorCategory|lower in colorHeading -%}
    	{%- set customColor = annot.colorCategory|lower -%}
    {%- else -%}
	    {%- set customColor = "other" -%}
    {%- endif -%}

    {%- set newAnnotations = (newAnnotations.push({"annotation": annot, "customColor": customColor}), newAnnotations) -%}

{%- endfor -%}

{#- INSERT ANNOTATIONS -#}

{%- for entry in newAnnotations -%}
{%- set annot = entry.annotation -%}

{%- if entry.customColor == 'red' %}
## {{annot.annotatedText|nl2br}}

{%- elif entry.customColor == 'magenta' %}
### {{annot.annotatedText|nl2br}}
{%- endif %}


{%- if entry.customColor != 'red' and entry.customColor != 'magenta' %}

> [!quote{{"|" + entry.customColor if entry.customColor!= "other"}}]+ {{calloutHeader(annot.type)}} {{colorHeading[entry.customColor]}} ([page. {{annot.pageLabel}}](zotero://open-pdf/library/items/{{annot.attachment.itemKey}}?page={{annot.pageLabel}}&annotation={{annot.id}}))

{%- if annot.annotatedText %}
> {{annot.annotatedText|nl2br}} {% if annot.hashTags %}{{annot.hashTags}}{% endif -%}
{%- endif %}

{%- if annot.imageRelativePath %}
> ![[{{annot.imageRelativePath}}]]
{%- endif %}

{%- if annot.ocrText %}
> {{annot.ocrText}}
{%- endif %}

{%- if annot.comment %}
> - **{{annot.comment|nl2br}}**
{%- endif -%}

{%- endif -%}

{%- endfor -%}

{% endif %}
{% endpersist %}