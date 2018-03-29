---
layout: default
title:  "Viz Playground - basic data viz with Vega"
date:   2018-03-29
category: data visualisation
tags: [js, coding, data, data-viz, viz-playground, vega]
---

# Viz Playground - basic data viz with Vega


Oh no :-/ I haven't written a post in so long!

For my second technical post, I had in mind something related to the talk I gave at [PyCon Sweden 2017](http://pycon.se/) back in September 2017 (here's a [link](https://github.com/Eleonore9/pyconse17/blob/master/slides-ele-pyconse17.pdf) to my slides on Github).
My idea then was to share my experience of **trying a new data visualisation tool**. This tool called [**Altair**](https://github.com/altair-viz/altair) isn't another data visualisation library as such, but a Python API to access the powerful and simple [**Vega-Lite**](https://github.com/vega/vega-lite) **JSON like grammar for interactive graphics** (itself build on top of **Vega**).

## Why Vega?

While it was fun and interesting to try out **Altair** (I used it for a personal [project](https://eleonore9.github.io/ebola_outbreaks/)), it is still in the process of supporting Vega-Lite 2.0. So, I now decided to directly try out **Vega**.

The idea is still to have a go-to tool for creating a rapid data visualisation. Specific use cases would be a prototype, a hackathon project... Come on, don't tell me you don't have a dataset of interest to visualise and share with a colleague or the world (well, your Twitter followers at least)!

Vega generates web visualisation using either HTML5 Canvas or SVG. And here we're working with **Vega-Lite** which provides a more concise syntax than **Vega**.


## Create a Vega-Lite chart
*Note:* You can have a look at the plot on [Blocks](https://bl.ocks.org/Eleonore9/0a759cc56022e40930530f32a0d92525)!

**Vega-Lite** visualisations are specified by JSON objects that can contain several properties like "name", "description", "title", "data", "transform", etc...

To define a minimal chart you want to provide the "data", "mark" and "encoding" properties.

* The `data` source is expected to be tabular data. It can be inline data (see below) or loaded from a file (JSON, CSV or TSV). [1]
* The `mark` is the type of visualisation you need, in this case "bar" for a bar chart. It can be a simple string or a `mark` object. [2]
* The `encoding` property links the data fields and the axis of the chart. In our case we're creating an horizontal bar chart, so we're inverting "x" and "y". Here the "y" axis will display the "element" which is an "ordinal" data type as I'm using it as a label for each data point and want to keep the order. And the "x" axis will display the actual "value" field hence the type is "quantitative".
The `encoding` can have more properties to transform the data. [3]

{% highlight javascript %}
{
  "data": {
            "values": [{"element": 1, "value": 4},
	               {"element": 2, "value": 8},
	               {"element": 3, "value": 15},
	               {"element": 4, "value": 16},
	               {"element": 5, "value": 23},
	               {"element": 6, "value": 42}]
	  },
  "mark": "bar",
  "encoding": {
    "y": {
      "field": "element",
      "type": "ordinal"
    },
    "x": {
      "field": "value",
      "type": "quantitative"
    }
  }
}
{% endhighlight %}
You can copy/paste this code in a [Vega editor](https://vega.github.io/editor/)!

## Embed a Vega-Lite chart in html

The easier way to publish a Vega-Lite visualisation is to embed it using the libray [Vega-Embed](https://github.com/vega/vega-embed).

1) Add the CDN (content delivery network) links in your HTML page:
{% highlight html %}
<script src="https://cdn.jsdelivr.net/npm/vega@3"></script>
<script src="https://cdn.jsdelivr.net/npm/vega-lite@2"></script>
<script src="https://cdn.jsdelivr.net/npm/vega-embed@3"></script>
{% endhighlight %}

2) Add a *div* where the visualisation will be attached `<div id="vis"></div>`.

3) Link the `#vis` div to the Vega-Lite `chart` object using the function *vegaEmbed()*, like this: `vegaEmbed("#vis", chart);`.

The `chart` object to be embedded contains "data", "mark" and "encoding" properties as well as an optional JSON schema, "$schema", and an optional "description".

In my example below you can see the *vegaEmbed* function can take an extra optional property (called `opt` below). This gives you more control on the rendering of the visualisation.
Here I choose to render a SVG (instead of a PNG image) and I specify its size.

{% highlight javascript %}
var chart = {
	  "$schema": "https://vega.github.io/schema/vega-lite/v2.json",
	  "description": "A simple bar chart with embedded data.",
	  "data": {
            "values": [{"element": 1, "value": 4},
	               {"element": 2, "value": 8},
	               {"element": 3, "value": 15},
	               {"element": 4, "value": 16},
	               {"element": 5, "value": 23},
	               {"element": 6, "value": 42}]
	  },
	  "mark": "bar",
	  "encoding": {
            "y": { "field": "element", "type": "ordinal" },
            "x": { "field": "value", "type": "quantitative" }
	  }
	  };

var opt = {
	  "mode": "vega-lite",
	  "renderer": "svg",
	  "width": 450,
	  "height": 450
	  };

vegaEmbed("#vis", chart, opt).then(function (result) {
	  }).catch(console.error);
{% endhighlight %}

This code creates the following chart:

![horizontal barchart]({{ "/assets/vega.svg" | absolute_url }})

## Minimal interaction

I thought it would help the user experience to add a display of information about a data point when one hovers over said data point. For that I used the Vega plugin called [Tooltip](https://github.com/vega/vega-tooltip).

There isn't much more to do once the chart is embedded in htlm.

1) Add links to Tooltip javascript and CSS CDNs:

{% highlight html %}
<script src="https://cdn.jsdelivr.net/npm/vega-tooltip@[VERSION]"></script>
<link rel="stylesheet" type="text/css" href="https://cdn.jsdelivr.net/npm/vega-tooltip@[VERSION]/build/vega-tooltip.min.css">
{% endhighlight %}

2) Create a tooltip using the *vegaTooltip.vegaLite()* function using the Vega view and the Vega-Lite chart object:

{% highlight javascript %}
vegaEmbed("#vis", chart, opt).then(function (result) {
      vegaTooltip.vegaLite(result.view, chart);
	  }).catch(console.error);
{% endhighlight %}

Here's what the tooltip looks like:

![gif of horizontal barchart with tooltip]({{ "/assets/vega-tooltip.gif" | absolute_url }})

And interact with it [here](https://bl.ocks.org/Eleonore9/raw/0a759cc56022e40930530f32a0d92525/).

## My take away

I find Vega-Lite grammar super nice! The usage is straightforward and it's very well documented! I'm definitely going to use it more in the future.

I'm also looking forward to the new version of [Altair](https://github.com/altair-viz/altair) and I'll try the Clojure API for Vega called [Oz](https://github.com/metasoarous/oz).

_____
Notes:

[1] Vega-Lite `data` property: [vega.github.io/vega-lite/docs/data.html](https://vega.github.io/vega-lite/docs/data.html)

[2] Vega-Lite `mark` property: [vega.github.io/vega-lite/docs/mark.html](https://vega.github.io/vega-lite/docs/mark.html)

[3] Vega-Lite `encoding` property: [vega.github.io/vega-lite/docs/encoding.html](https://vega.github.io/vega-lite/docs/encoding.html)


References:

* More on Vega and Vega-Lite: [vega.github.io/](https://vega.github.io/)

* Vega in Python with [Altair](https://github.com/altair-viz/altair) and in Clojure with [Oz](https://github.com/metasoarous/oz)

* Code and visualisation done for this blog post on [Blocks](https://bl.ocks.org/Eleonore9/0a759cc56022e40930530f32a0d92525)

* Read up options to embed Vega visualisations on [Vega-embed](https://github.com/vega/vega-embed) Readme.

* The Vega [Tooltip](https://github.com/vega/vega-tooltip) plugin.
