---
title: "On tests, sensitivity, and specificity"
linktitle: "On tests, sensitivity, and specificity"
date: 2020-04-07
lastmod: 2020-11-29
draft: false
categories: ["Mathematics"]
---
*Disclaimer*: This page neds javascript to render LaTeX and Bokeh content.

I few days ago I was reading a Spanish newspaper and one of the headlines was how the government had acquired some tests with a sensitivity of 30% [1]. The article indicated that this was bad. But how bad? I wondered. What does it mean exactly?

In this entry I will explain the following terms:

- Sensitivity, also known as true positive rate, the recall, or probability of detection.
- Specificity, also known as true negative rate.
- Predictive Positive Value, also known as precision.
- Negative Predictive Value. I don't think NPV is known by any other name in classification.

For convenience I will be talking about tests and prevalence of a disease. But a test is a binary classifier as well, and this explanation can be extended to any binary classifier.

At the end of this entry I will also provide an *interactive widget* that you can use to play with all the settings in a sample population, in order to get a gut feeling of what these concepts really mean, and how they interact with each other.

## Basics

Let's start by providing some simple definitions that will be used to define the terms in a frequentist paradigm:

- *True Positive, TP*: A positive case labeled as positive.
- *False Positive,  FP*: A negative case labeled as positive.
- *True Negative, TN*: A negative case labeled as negative.
- *False negative, FN* : A positive case labeled as negative.

To understand the rest of the article we will only need these 4 definitions.

When talking about conditional probability, it's usefult to remember Baye's Rule:

$$ P(A|B)=\frac{P(B|A)P(A)}{P(B)} $$

To reiterate, it's not esential to understand the concepts in this article.

## Sensitivity and Specificity

The sensitivity of a test or, more generally, a binary classifier, is defined as the probability of the test to correctly identify the positive cases [2].

$$Sensitivity=\frac{TP}{TP+FN}$$

We can also express it as a conditional probability, by using Baye's Theorem. Let's say that `test` represents a positive result in the test, and `A` represents the sample actually being positive. In that case:

$$ P(test|A) = \frac{P(A|test)P(test)}{P(A)} $$

In a similar way, the specifity of a test is defined as the probability of correctly identifying those patients who are negative.

$$ Specificity=\frac{TN}{TN+FP} $$

It can also be expressed as:

$$ P(\bar{test}|\bar{A})=\frac{P(\bar{A}|\bar{test})P(\bar{test})}{P(\bar{A})} $$

This formulas might make sense to you, or not. But I don't think learning a formula by heart is the same as understanding the concept it represents. So let's try to show visually what these two concepts mean [3]

Below you will see a graph with a population of 100. Each one of the people in the population will either be green or red, and each one of them will have a dot representing a positive or negative test:

- A green circle means it is positive. A red circle means it is negative.
- A white dot means that the test shows a positive, and a black dot means that the test shows a negative.

This way, the 4 basic cases that we explained in *Basics* will be represented like this:

- TP: ![True Positive](/img/2020/TP.png)
- FN: ![False Negative](/img/2020/FN.png)
- TN: ![True Negative](/img/2020/TN.png)
- FP: ![False Positive](/img/2020/FP.png)

Let's assume that we have:

- Population of 100 people.
- 60% incidence of the disease.
- Sensitivity of 40%.
- Specificity of 70%.

![Example Population](/img/2020/example_population.png)

For the sensitivity of the test only the Positive cases are taken into account, this is, True Positives and False Negatives.

![Sensitivity](/img/2020/sensitivity.png)

$$ Sensitivity=\frac{TP}{TP+FN} $$

The sensitivity of a test is *independent* of the prevalence of the disease in the population. In this case we have 24 True Positives, and 36 False negatives. The sensitivity is telling us what percentage of the people that *actually have the disease* will also test positive. Hence: 24/(24+36)=40%.

For the specificity of the test only the Negative cases are considered, this is, True Negatives and False Positives.

![Specificity](/img/2020/specificity.png)

$$ Specificity=\frac{TN}{TN+FP} $$

Specificity, like sensitivity, is *independent* of the prevalence of the disease in the populacion. We have 28 True Negatives and 12 False positives. The specificity is telling us what percentage of the people that *do no have the disease* will also test negative. Hence: 28/(28+12)=70%.

Remember that you can use the visualization at the end of the article to play with these settings.

## Negative Predictive Value and Positive Predictive Value

Imagine you are a doctor. You know that the sensitivity of the test you are using is 90%. But that *only* tells you the probablity of the result being positive *given* that the patient is positive. But you want the opposite! You want to know if the patient is positive (or negative) once you get the results of the test. 

This is what the negative and positive predictive values represent.

The Positive Predictive Value (PPV) gives us the probability of a patent being positive *after* having tested positive.

$$ PPV=\frac{TP}{FP+FP} $$

And from a Bayesian paradigm [4]:

$$ PPV=P(A|test)=\frac{sensitivity \cdot P(A)}{sensitivity \cdot P(A) + (1-specificity)(1-P(\bar{A}))} $$

(Exercise for the reader: try to derive it yourself!)

Conversely the Predictive Negative Value represents how likelly is that a patient will be negative given that the test result is negative:

$$ NPV = \frac{TN}{TN+FN} $$

or:

$$ PPV=P(\bar{A}|\bar{test})=\frac{specificity \cdot (1-P(A))}{(1-sensitivity)P(A)+specificity(1-P(A))} $$

One interesting observation that we can already make is that PPV and NPV depend on the incidence of the disease in the population.

This means that a test with a particular sensitivity and specificity will have different PPV/NPV depending on where it's used.

Let's see how PPV and NPV relate to our population

![PPV](/img/2020/PPV.png)

All the information contained in the blue regions is what we need for to calculate the PPV. And it's easy to see what it's doing. From all the test positive cases, we are only interested in the True Positives. Dividing them by the total number of test positives, gives us the PPV.

In a similar way, the test negative cases, contained in the orange rectangle, are used to calculate the NPV.

And we see as well why both of them are dependant on the prevalence in the population.

Given a fixed sensitivity and specificity, the higher the prevalence, the higher TP and the lower FP, and in consequence, the higher PPV is.

The lower the prevalence, the higher TN and the lower FN are, and the higher the NPV is.

## Interactive visualization

I wrote a small visualization using Python and Javascript with Bokeh. It represents a population of 100 people and you can modify the prevalence, the sensitivity, and the specificity, and see how it affects all these numbers we have talked about. Play with it, and see if it helps you really graps these concepts.

{{< rawhtml >}}
<script src="https://cdn.bokeh.org/bokeh/release/bokeh-2.0.1.min.js"
        crossorigin="anonymous"></script>
<script src="https://cdn.bokeh.org/bokeh/release/bokeh-widgets-2.0.1.min.js"
        crossorigin="anonymous"></script>
<script src="https://cdn.bokeh.org/bokeh/release/bokeh-tables-2.0.1.min.js"
        crossorigin="anonymous"></script>
		
<script type="text/javascript">

    (function() {
          var fn = function() {
            Bokeh.safely(function() {
              (function(root) {
                function embed_document(root) {
                  
                var docs_json = '{&quot;d3092d28-506f-4eaf-bcf8-db53a2d12ab0&quot;:{&quot;roots&quot;:{&quot;references&quot;:[{&quot;attributes&quot;:{&quot;text&quot;:&quot;TP = 50 \\t FP = 0&quot;},&quot;id&quot;:&quot;1072&quot;,&quot;type&quot;:&quot;PreText&quot;},{&quot;attributes&quot;:{&quot;bottom_units&quot;:&quot;screen&quot;,&quot;fill_alpha&quot;:0.5,&quot;fill_color&quot;:&quot;lightgrey&quot;,&quot;left_units&quot;:&quot;screen&quot;,&quot;level&quot;:&quot;overlay&quot;,&quot;line_alpha&quot;:1.0,&quot;line_color&quot;:&quot;black&quot;,&quot;line_dash&quot;:[4,4],&quot;line_width&quot;:2,&quot;render_mode&quot;:&quot;css&quot;,&quot;right_units&quot;:&quot;screen&quot;,&quot;top_units&quot;:&quot;screen&quot;},&quot;id&quot;:&quot;1030&quot;,&quot;type&quot;:&quot;BoxAnnotation&quot;},{&quot;attributes&quot;:{&quot;children&quot;:[{&quot;id&quot;:&quot;1007&quot;},{&quot;id&quot;:&quot;1070&quot;},{&quot;id&quot;:&quot;1068&quot;},{&quot;id&quot;:&quot;1069&quot;}]},&quot;id&quot;:&quot;1079&quot;,&quot;type&quot;:&quot;Column&quot;},{&quot;attributes&quot;:{&quot;text&quot;:&quot;TN = 50 \\t FN = 0&quot;},&quot;id&quot;:&quot;1073&quot;,&quot;type&quot;:&quot;PreText&quot;},{&quot;attributes&quot;:{&quot;fill_color&quot;:{&quot;value&quot;:&quot;lightgreen&quot;},&quot;line_color&quot;:{&quot;value&quot;:&quot;lightgreen&quot;},&quot;radius&quot;:{&quot;units&quot;:&quot;data&quot;,&quot;value&quot;:0.45},&quot;x&quot;:{&quot;field&quot;:&quot;x_values&quot;},&quot;y&quot;:{&quot;field&quot;:&quot;y_values&quot;}},&quot;id&quot;:&quot;1039&quot;,&quot;type&quot;:&quot;Circle&quot;},{&quot;attributes&quot;:{&quot;fill_color&quot;:{&quot;value&quot;:&quot;white&quot;},&quot;line_color&quot;:{&quot;value&quot;:&quot;white&quot;},&quot;radius&quot;:{&quot;units&quot;:&quot;data&quot;,&quot;value&quot;:0.1},&quot;x&quot;:{&quot;field&quot;:&quot;x_values&quot;},&quot;y&quot;:{&quot;field&quot;:&quot;y_values&quot;}},&quot;id&quot;:&quot;1049&quot;,&quot;type&quot;:&quot;Circle&quot;},{&quot;attributes&quot;:{&quot;text&quot;:&quot;PPV = 1 \\t NPV = 1&quot;},&quot;id&quot;:&quot;1074&quot;,&quot;type&quot;:&quot;PreText&quot;},{&quot;attributes&quot;:{},&quot;id&quot;:&quot;1089&quot;,&quot;type&quot;:&quot;Selection&quot;},{&quot;attributes&quot;:{&quot;data_source&quot;:{&quot;id&quot;:&quot;1002&quot;},&quot;glyph&quot;:{&quot;id&quot;:&quot;1044&quot;},&quot;hover_glyph&quot;:null,&quot;muted_glyph&quot;:null,&quot;nonselection_glyph&quot;:{&quot;id&quot;:&quot;1045&quot;},&quot;selection_glyph&quot;:null,&quot;view&quot;:{&quot;id&quot;:&quot;1047&quot;}},&quot;id&quot;:&quot;1046&quot;,&quot;type&quot;:&quot;GlyphRenderer&quot;},{&quot;attributes&quot;:{&quot;args&quot;:{&quot;source_negative&quot;:{&quot;id&quot;:&quot;1002&quot;},&quot;source_positive&quot;:{&quot;id&quot;:&quot;1001&quot;}},&quot;code&quot;:&quot;\\n    // If you are reading this, I know I know, it&#x27;s not pretty.\\n    // I was going to rewrite it once I made sure it&#x27;s working. But you know \\n    // how it goes...\\n    var pos_data = source_positive.data;\\n    var neg_data = source_negative.data;\\n    var positives = cb_obj.value;\\n    pos_data[&#x27;x_values&#x27;]=[];\\n    pos_data[&#x27;y_values&#x27;]=[];\\n    var pos_quotient = Math.floor(positives/10);\\n    var pos_remainder = positives % 10;\\n    var row;\\n    var col;\\n    for (row=0; row&lt;pos_quotient; row++){\\n        for (col=0; col&lt;10; col++){\\n            pos_data[&#x27;x_values&#x27;].push(col)\\n        }\\n    }\\n    for (col=0; col&lt;pos_quotient; col++){\\n        for (row=0; row&lt;10; row++){\\n            pos_data[&#x27;y_values&#x27;].push(col)\\n        }\\n    }\\n    for (col=0; col&lt;pos_remainder; col++){\\n        pos_data[&#x27;x_values&#x27;].push(col)\\n        pos_data[&#x27;y_values&#x27;].push(pos_quotient)\\n    }\\n    neg_data[&#x27;x_values&#x27;]=[];\\n    neg_data[&#x27;y_values&#x27;]=[];\\n    var negatives = 100 - positives;\\n    var neg_quotient = Math.floor(negatives/10);\\n    var neg_remainder = negatives % 10;\\n    for (row=0; row&lt;neg_quotient; row++){\\n        for (col=0; col&lt;10; col++){\\n            neg_data[&#x27;x_values&#x27;].push(col)\\n        }\\n    }\\n    for (col=10-neg_quotient; col&lt;10; col++){\\n        for (row=0; row&lt;10; row++){\\n            neg_data[&#x27;y_values&#x27;].push(col)\\n        }\\n    }\\n    for (col=10-neg_remainder; col&lt;10; col++){\\n        neg_data[&#x27;x_values&#x27;].push(col)\\n        neg_data[&#x27;y_values&#x27;].push(9-neg_quotient)\\n    }\\n    source_positive.change.emit();\\n    source_negative.change.emit();\\n&quot;},&quot;id&quot;:&quot;1075&quot;,&quot;type&quot;:&quot;CustomJS&quot;},{&quot;attributes&quot;:{},&quot;id&quot;:&quot;1010&quot;,&quot;type&quot;:&quot;DataRange1d&quot;},{&quot;attributes&quot;:{},&quot;id&quot;:&quot;1014&quot;,&quot;type&quot;:&quot;LinearScale&quot;},{&quot;attributes&quot;:{},&quot;id&quot;:&quot;1087&quot;,&quot;type&quot;:&quot;BasicTickFormatter&quot;},{&quot;attributes&quot;:{&quot;below&quot;:[{&quot;id&quot;:&quot;1016&quot;}],&quot;center&quot;:[{&quot;id&quot;:&quot;1019&quot;},{&quot;id&quot;:&quot;1023&quot;}],&quot;left&quot;:[{&quot;id&quot;:&quot;1020&quot;}],&quot;renderers&quot;:[{&quot;id&quot;:&quot;1041&quot;},{&quot;id&quot;:&quot;1046&quot;},{&quot;id&quot;:&quot;1051&quot;},{&quot;id&quot;:&quot;1056&quot;},{&quot;id&quot;:&quot;1061&quot;},{&quot;id&quot;:&quot;1066&quot;}],&quot;title&quot;:{&quot;id&quot;:&quot;1083&quot;},&quot;toolbar&quot;:{&quot;id&quot;:&quot;1031&quot;},&quot;x_range&quot;:{&quot;id&quot;:&quot;1008&quot;},&quot;x_scale&quot;:{&quot;id&quot;:&quot;1012&quot;},&quot;y_range&quot;:{&quot;id&quot;:&quot;1010&quot;},&quot;y_scale&quot;:{&quot;id&quot;:&quot;1014&quot;}},&quot;id&quot;:&quot;1007&quot;,&quot;subtype&quot;:&quot;Figure&quot;,&quot;type&quot;:&quot;Plot&quot;},{&quot;attributes&quot;:{&quot;args&quot;:{&quot;slider_sensitivity&quot;:{&quot;id&quot;:&quot;1068&quot;},&quot;source_false_negatives&quot;:{&quot;id&quot;:&quot;1004&quot;},&quot;source_positive&quot;:{&quot;id&quot;:&quot;1001&quot;},&quot;source_true_positives&quot;:{&quot;id&quot;:&quot;1003&quot;}},&quot;code&quot;:&quot;\\n    var pos_data = source_positive.data;\\n    var test_pos_data = source_true_positives.data;\\n    var test_neg_data = source_false_negatives.data;\\n    var sensitivity = slider_sensitivity.value;\\n    test_pos_data[&#x27;x_values&#x27;]=[];\\n    test_pos_data[&#x27;y_values&#x27;]=[];\\n    test_neg_data[&#x27;x_values&#x27;]=[];\\n    test_neg_data[&#x27;y_values&#x27;]=[];\\n    var num_true_positives = Math.floor(pos_data[&#x27;x_values&#x27;].length * sensitivity);\\n    var num_positives = pos_data[&#x27;x_values&#x27;].length;\\n    var idx;\\n    var row=0;\\n    var col=0;\\n    for (idx=0;idx&lt;num_positives; idx++){\\n        if (idx&lt;num_true_positives) {\\n            test_pos_data[&#x27;x_values&#x27;].push(col)\\n            test_pos_data[&#x27;y_values&#x27;].push(row)\\n        } else {\\n            test_neg_data[&#x27;x_values&#x27;].push(col)\\n            test_neg_data[&#x27;y_values&#x27;].push(row)\\n        }\\n        col = col + 1;\\n        if (col == 10) {\\n            col =0\\n            row = row+1\\n        }\\n    }\\n    source_true_positives.change.emit();\\n    source_false_negatives.change.emit();\\n&quot;},&quot;id&quot;:&quot;1076&quot;,&quot;type&quot;:&quot;CustomJS&quot;},{&quot;attributes&quot;:{&quot;children&quot;:[{&quot;id&quot;:&quot;1079&quot;},{&quot;id&quot;:&quot;1080&quot;}]},&quot;id&quot;:&quot;1081&quot;,&quot;type&quot;:&quot;Row&quot;},{&quot;attributes&quot;:{},&quot;id&quot;:&quot;1012&quot;,&quot;type&quot;:&quot;LinearScale&quot;},{&quot;attributes&quot;:{},&quot;id&quot;:&quot;1090&quot;,&quot;type&quot;:&quot;UnionRenderers&quot;},{&quot;attributes&quot;:{},&quot;id&quot;:&quot;1017&quot;,&quot;type&quot;:&quot;BasicTicker&quot;},{&quot;attributes&quot;:{&quot;source&quot;:{&quot;id&quot;:&quot;1002&quot;}},&quot;id&quot;:&quot;1047&quot;,&quot;type&quot;:&quot;CDSView&quot;},{&quot;attributes&quot;:{&quot;formatter&quot;:{&quot;id&quot;:&quot;1085&quot;},&quot;ticker&quot;:{&quot;id&quot;:&quot;1017&quot;}},&quot;id&quot;:&quot;1016&quot;,&quot;type&quot;:&quot;LinearAxis&quot;},{&quot;attributes&quot;:{},&quot;id&quot;:&quot;1091&quot;,&quot;type&quot;:&quot;Selection&quot;},{&quot;attributes&quot;:{&quot;fill_alpha&quot;:{&quot;value&quot;:0.1},&quot;fill_color&quot;:{&quot;value&quot;:&quot;red&quot;},&quot;line_alpha&quot;:{&quot;value&quot;:0.1},&quot;line_color&quot;:{&quot;value&quot;:&quot;red&quot;},&quot;radius&quot;:{&quot;units&quot;:&quot;data&quot;,&quot;value&quot;:0.45},&quot;x&quot;:{&quot;field&quot;:&quot;x_values&quot;},&quot;y&quot;:{&quot;field&quot;:&quot;y_values&quot;}},&quot;id&quot;:&quot;1045&quot;,&quot;type&quot;:&quot;Circle&quot;},{&quot;attributes&quot;:{},&quot;id&quot;:&quot;1092&quot;,&quot;type&quot;:&quot;UnionRenderers&quot;},{&quot;attributes&quot;:{&quot;data&quot;:{&quot;x_values&quot;:[],&quot;y_values&quot;:[]},&quot;selected&quot;:{&quot;id&quot;:&quot;1095&quot;},&quot;selection_policy&quot;:{&quot;id&quot;:&quot;1096&quot;}},&quot;id&quot;:&quot;1006&quot;,&quot;type&quot;:&quot;ColumnDataSource&quot;},{&quot;attributes&quot;:{&quot;data_source&quot;:{&quot;id&quot;:&quot;1003&quot;},&quot;glyph&quot;:{&quot;id&quot;:&quot;1049&quot;},&quot;hover_glyph&quot;:null,&quot;muted_glyph&quot;:null,&quot;nonselection_glyph&quot;:{&quot;id&quot;:&quot;1050&quot;},&quot;selection_glyph&quot;:null,&quot;view&quot;:{&quot;id&quot;:&quot;1052&quot;}},&quot;id&quot;:&quot;1051&quot;,&quot;type&quot;:&quot;GlyphRenderer&quot;},{&quot;attributes&quot;:{},&quot;id&quot;:&quot;1093&quot;,&quot;type&quot;:&quot;Selection&quot;},{&quot;attributes&quot;:{&quot;fill_color&quot;:{&quot;value&quot;:&quot;white&quot;},&quot;line_color&quot;:{&quot;value&quot;:&quot;white&quot;},&quot;radius&quot;:{&quot;units&quot;:&quot;data&quot;,&quot;value&quot;:0.1},&quot;x&quot;:{&quot;field&quot;:&quot;x_values&quot;},&quot;y&quot;:{&quot;field&quot;:&quot;y_values&quot;}},&quot;id&quot;:&quot;1054&quot;,&quot;type&quot;:&quot;Circle&quot;},{&quot;attributes&quot;:{&quot;data_source&quot;:{&quot;id&quot;:&quot;1005&quot;},&quot;glyph&quot;:{&quot;id&quot;:&quot;1064&quot;},&quot;hover_glyph&quot;:null,&quot;muted_glyph&quot;:null,&quot;nonselection_glyph&quot;:{&quot;id&quot;:&quot;1065&quot;},&quot;selection_glyph&quot;:null,&quot;view&quot;:{&quot;id&quot;:&quot;1067&quot;}},&quot;id&quot;:&quot;1066&quot;,&quot;type&quot;:&quot;GlyphRenderer&quot;},{&quot;attributes&quot;:{},&quot;id&quot;:&quot;1094&quot;,&quot;type&quot;:&quot;UnionRenderers&quot;},{&quot;attributes&quot;:{},&quot;id&quot;:&quot;1021&quot;,&quot;type&quot;:&quot;BasicTicker&quot;},{&quot;attributes&quot;:{&quot;axis&quot;:{&quot;id&quot;:&quot;1016&quot;},&quot;ticker&quot;:null},&quot;id&quot;:&quot;1019&quot;,&quot;type&quot;:&quot;Grid&quot;},{&quot;attributes&quot;:{},&quot;id&quot;:&quot;1095&quot;,&quot;type&quot;:&quot;Selection&quot;},{&quot;attributes&quot;:{&quot;source&quot;:{&quot;id&quot;:&quot;1003&quot;}},&quot;id&quot;:&quot;1052&quot;,&quot;type&quot;:&quot;CDSView&quot;},{&quot;attributes&quot;:{&quot;axis&quot;:{&quot;id&quot;:&quot;1020&quot;},&quot;dimension&quot;:1,&quot;ticker&quot;:null},&quot;id&quot;:&quot;1023&quot;,&quot;type&quot;:&quot;Grid&quot;},{&quot;attributes&quot;:{&quot;args&quot;:{&quot;slider_population&quot;:{&quot;id&quot;:&quot;1070&quot;},&quot;source_false_negatives&quot;:{&quot;id&quot;:&quot;1004&quot;},&quot;source_false_positives&quot;:{&quot;id&quot;:&quot;1006&quot;},&quot;source_true_negatives&quot;:{&quot;id&quot;:&quot;1005&quot;},&quot;source_true_positives&quot;:{&quot;id&quot;:&quot;1003&quot;},&quot;textPN&quot;:{&quot;id&quot;:&quot;1071&quot;},&quot;textPPVNPV&quot;:{&quot;id&quot;:&quot;1074&quot;},&quot;textTNFN&quot;:{&quot;id&quot;:&quot;1073&quot;},&quot;textTPFP&quot;:{&quot;id&quot;:&quot;1072&quot;}},&quot;code&quot;:&quot;\\n    var positives = slider_population.value;\\n    var negatives = 100 - positives;\\n    var TP = source_true_positives.data[&#x27;x_values&#x27;].length;\\n    var FP = source_false_positives.data[&#x27;x_values&#x27;].length;\\n    var TN = source_true_negatives.data[&#x27;x_values&#x27;].length;\\n    var FN = source_false_negatives.data[&#x27;x_values&#x27;].length;\\n    var PPV = (parseFloat(TP)/(TP+FP)).toFixed(2);\\n    var NPV = (parseFloat(TN)/(TN+FN)).toFixed(2);\\n    textPN.text = \\&quot;\\&quot;.concat(\\&quot;Positives = \\&quot;, positives.toString(),\\&quot;\\t\\&quot;,\\&quot;Negatives = \\&quot;,negatives.toString());\\n    textTPFP.text = \\&quot;\\&quot;.concat(\\&quot;TP = \\&quot;, TP.toString(), \\&quot;\\t\\&quot;, \\&quot;FP = \\&quot;, FP.toString());\\n    textTNFN.text = \\&quot;\\&quot;.concat(\\&quot;TN = \\&quot;, TN.toString(), \\&quot;\\t\\&quot;, \\&quot;FN = \\&quot;, FN.toString());\\n    textPPVNPV.text = \\&quot;\\&quot;.concat(\\&quot;PPV = \\&quot;, PPV.toString(), \\&quot;\\t\\&quot;, \\&quot;NPV = \\&quot;, NPV.toString());\\n&quot;},&quot;id&quot;:&quot;1078&quot;,&quot;type&quot;:&quot;CustomJS&quot;},{&quot;attributes&quot;:{},&quot;id&quot;:&quot;1096&quot;,&quot;type&quot;:&quot;UnionRenderers&quot;},{&quot;attributes&quot;:{&quot;formatter&quot;:{&quot;id&quot;:&quot;1087&quot;},&quot;ticker&quot;:{&quot;id&quot;:&quot;1021&quot;}},&quot;id&quot;:&quot;1020&quot;,&quot;type&quot;:&quot;LinearAxis&quot;},{&quot;attributes&quot;:{&quot;fill_alpha&quot;:{&quot;value&quot;:0.1},&quot;fill_color&quot;:{&quot;value&quot;:&quot;white&quot;},&quot;line_alpha&quot;:{&quot;value&quot;:0.1},&quot;line_color&quot;:{&quot;value&quot;:&quot;white&quot;},&quot;radius&quot;:{&quot;units&quot;:&quot;data&quot;,&quot;value&quot;:0.1},&quot;x&quot;:{&quot;field&quot;:&quot;x_values&quot;},&quot;y&quot;:{&quot;field&quot;:&quot;y_values&quot;}},&quot;id&quot;:&quot;1050&quot;,&quot;type&quot;:&quot;Circle&quot;},{&quot;attributes&quot;:{},&quot;id&quot;:&quot;1097&quot;,&quot;type&quot;:&quot;Selection&quot;},{&quot;attributes&quot;:{&quot;data_source&quot;:{&quot;id&quot;:&quot;1006&quot;},&quot;glyph&quot;:{&quot;id&quot;:&quot;1054&quot;},&quot;hover_glyph&quot;:null,&quot;muted_glyph&quot;:null,&quot;nonselection_glyph&quot;:{&quot;id&quot;:&quot;1055&quot;},&quot;selection_glyph&quot;:null,&quot;view&quot;:{&quot;id&quot;:&quot;1057&quot;}},&quot;id&quot;:&quot;1056&quot;,&quot;type&quot;:&quot;GlyphRenderer&quot;},{&quot;attributes&quot;:{},&quot;id&quot;:&quot;1098&quot;,&quot;type&quot;:&quot;UnionRenderers&quot;},{&quot;attributes&quot;:{&quot;fill_color&quot;:{&quot;value&quot;:&quot;black&quot;},&quot;radius&quot;:{&quot;units&quot;:&quot;data&quot;,&quot;value&quot;:0.1},&quot;x&quot;:{&quot;field&quot;:&quot;x_values&quot;},&quot;y&quot;:{&quot;field&quot;:&quot;y_values&quot;}},&quot;id&quot;:&quot;1059&quot;,&quot;type&quot;:&quot;Circle&quot;},{&quot;attributes&quot;:{&quot;source&quot;:{&quot;id&quot;:&quot;1001&quot;}},&quot;id&quot;:&quot;1042&quot;,&quot;type&quot;:&quot;CDSView&quot;},{&quot;attributes&quot;:{&quot;fill_color&quot;:{&quot;value&quot;:&quot;black&quot;},&quot;radius&quot;:{&quot;units&quot;:&quot;data&quot;,&quot;value&quot;:0.1},&quot;x&quot;:{&quot;field&quot;:&quot;x_values&quot;},&quot;y&quot;:{&quot;field&quot;:&quot;y_values&quot;}},&quot;id&quot;:&quot;1064&quot;,&quot;type&quot;:&quot;Circle&quot;},{&quot;attributes&quot;:{},&quot;id&quot;:&quot;1099&quot;,&quot;type&quot;:&quot;Selection&quot;},{&quot;attributes&quot;:{&quot;fill_color&quot;:{&quot;value&quot;:&quot;red&quot;},&quot;line_color&quot;:{&quot;value&quot;:&quot;red&quot;},&quot;radius&quot;:{&quot;units&quot;:&quot;data&quot;,&quot;value&quot;:0.45},&quot;x&quot;:{&quot;field&quot;:&quot;x_values&quot;},&quot;y&quot;:{&quot;field&quot;:&quot;y_values&quot;}},&quot;id&quot;:&quot;1044&quot;,&quot;type&quot;:&quot;Circle&quot;},{&quot;attributes&quot;:{},&quot;id&quot;:&quot;1008&quot;,&quot;type&quot;:&quot;DataRange1d&quot;},{&quot;attributes&quot;:{},&quot;id&quot;:&quot;1100&quot;,&quot;type&quot;:&quot;UnionRenderers&quot;},{&quot;attributes&quot;:{&quot;source&quot;:{&quot;id&quot;:&quot;1006&quot;}},&quot;id&quot;:&quot;1057&quot;,&quot;type&quot;:&quot;CDSView&quot;},{&quot;attributes&quot;:{&quot;data_source&quot;:{&quot;id&quot;:&quot;1001&quot;},&quot;glyph&quot;:{&quot;id&quot;:&quot;1039&quot;},&quot;hover_glyph&quot;:null,&quot;muted_glyph&quot;:null,&quot;nonselection_glyph&quot;:{&quot;id&quot;:&quot;1040&quot;},&quot;selection_glyph&quot;:null,&quot;view&quot;:{&quot;id&quot;:&quot;1042&quot;}},&quot;id&quot;:&quot;1041&quot;,&quot;type&quot;:&quot;GlyphRenderer&quot;},{&quot;attributes&quot;:{},&quot;id&quot;:&quot;1024&quot;,&quot;type&quot;:&quot;PanTool&quot;},{&quot;attributes&quot;:{&quot;data&quot;:{&quot;x_values&quot;:[0,1,2,3,4,5,6,7,8,9,0,1,2,3,4,5,6,7,8,9,0,1,2,3,4,5,6,7,8,9,0,1,2,3,4,5,6,7,8,9,0,1,2,3,4,5,6,7,8,9],&quot;y_values&quot;:[0,0,0,0,0,0,0,0,0,0,1,1,1,1,1,1,1,1,1,1,2,2,2,2,2,2,2,2,2,2,3,3,3,3,3,3,3,3,3,3,4,4,4,4,4,4,4,4,4,4]},&quot;selected&quot;:{&quot;id&quot;:&quot;1089&quot;},&quot;selection_policy&quot;:{&quot;id&quot;:&quot;1090&quot;}},&quot;id&quot;:&quot;1001&quot;,&quot;type&quot;:&quot;ColumnDataSource&quot;},{&quot;attributes&quot;:{&quot;fill_alpha&quot;:{&quot;value&quot;:0.1},&quot;fill_color&quot;:{&quot;value&quot;:&quot;white&quot;},&quot;line_alpha&quot;:{&quot;value&quot;:0.1},&quot;line_color&quot;:{&quot;value&quot;:&quot;white&quot;},&quot;radius&quot;:{&quot;units&quot;:&quot;data&quot;,&quot;value&quot;:0.1},&quot;x&quot;:{&quot;field&quot;:&quot;x_values&quot;},&quot;y&quot;:{&quot;field&quot;:&quot;y_values&quot;}},&quot;id&quot;:&quot;1055&quot;,&quot;type&quot;:&quot;Circle&quot;},{&quot;attributes&quot;:{&quot;data_source&quot;:{&quot;id&quot;:&quot;1004&quot;},&quot;glyph&quot;:{&quot;id&quot;:&quot;1059&quot;},&quot;hover_glyph&quot;:null,&quot;muted_glyph&quot;:null,&quot;nonselection_glyph&quot;:{&quot;id&quot;:&quot;1060&quot;},&quot;selection_glyph&quot;:null,&quot;view&quot;:{&quot;id&quot;:&quot;1062&quot;}},&quot;id&quot;:&quot;1061&quot;,&quot;type&quot;:&quot;GlyphRenderer&quot;},{&quot;attributes&quot;:{},&quot;id&quot;:&quot;1025&quot;,&quot;type&quot;:&quot;WheelZoomTool&quot;},{&quot;attributes&quot;:{&quot;data&quot;:{&quot;x_values&quot;:[],&quot;y_values&quot;:[]},&quot;selected&quot;:{&quot;id&quot;:&quot;1097&quot;},&quot;selection_policy&quot;:{&quot;id&quot;:&quot;1098&quot;}},&quot;id&quot;:&quot;1004&quot;,&quot;type&quot;:&quot;ColumnDataSource&quot;},{&quot;attributes&quot;:{&quot;overlay&quot;:{&quot;id&quot;:&quot;1030&quot;}},&quot;id&quot;:&quot;1026&quot;,&quot;type&quot;:&quot;BoxZoomTool&quot;},{&quot;attributes&quot;:{&quot;source&quot;:{&quot;id&quot;:&quot;1004&quot;}},&quot;id&quot;:&quot;1062&quot;,&quot;type&quot;:&quot;CDSView&quot;},{&quot;attributes&quot;:{},&quot;id&quot;:&quot;1027&quot;,&quot;type&quot;:&quot;SaveTool&quot;},{&quot;attributes&quot;:{},&quot;id&quot;:&quot;1028&quot;,&quot;type&quot;:&quot;ResetTool&quot;},{&quot;attributes&quot;:{&quot;children&quot;:[{&quot;id&quot;:&quot;1071&quot;},{&quot;id&quot;:&quot;1072&quot;},{&quot;id&quot;:&quot;1073&quot;},{&quot;id&quot;:&quot;1074&quot;}]},&quot;id&quot;:&quot;1080&quot;,&quot;type&quot;:&quot;Column&quot;},{&quot;attributes&quot;:{&quot;data&quot;:{&quot;x_values&quot;:[0,1,2,3,4,5,6,7,8,9,0,1,2,3,4,5,6,7,8,9,0,1,2,3,4,5,6,7,8,9,0,1,2,3,4,5,6,7,8,9,0,1,2,3,4,5,6,7,8,9],&quot;y_values&quot;:[5,5,5,5,5,5,5,5,5,5,6,6,6,6,6,6,6,6,6,6,7,7,7,7,7,7,7,7,7,7,8,8,8,8,8,8,8,8,8,8,9,9,9,9,9,9,9,9,9,9]},&quot;selected&quot;:{&quot;id&quot;:&quot;1099&quot;},&quot;selection_policy&quot;:{&quot;id&quot;:&quot;1100&quot;}},&quot;id&quot;:&quot;1005&quot;,&quot;type&quot;:&quot;ColumnDataSource&quot;},{&quot;attributes&quot;:{},&quot;id&quot;:&quot;1029&quot;,&quot;type&quot;:&quot;HelpTool&quot;},{&quot;attributes&quot;:{&quot;data&quot;:{&quot;x_values&quot;:[0,1,2,3,4,5,6,7,8,9,0,1,2,3,4,5,6,7,8,9,0,1,2,3,4,5,6,7,8,9,0,1,2,3,4,5,6,7,8,9,0,1,2,3,4,5,6,7,8,9],&quot;y_values&quot;:[5,5,5,5,5,5,5,5,5,5,6,6,6,6,6,6,6,6,6,6,7,7,7,7,7,7,7,7,7,7,8,8,8,8,8,8,8,8,8,8,9,9,9,9,9,9,9,9,9,9]},&quot;selected&quot;:{&quot;id&quot;:&quot;1091&quot;},&quot;selection_policy&quot;:{&quot;id&quot;:&quot;1092&quot;}},&quot;id&quot;:&quot;1002&quot;,&quot;type&quot;:&quot;ColumnDataSource&quot;},{&quot;attributes&quot;:{&quot;fill_alpha&quot;:{&quot;value&quot;:0.1},&quot;fill_color&quot;:{&quot;value&quot;:&quot;black&quot;},&quot;line_alpha&quot;:{&quot;value&quot;:0.1},&quot;radius&quot;:{&quot;units&quot;:&quot;data&quot;,&quot;value&quot;:0.1},&quot;x&quot;:{&quot;field&quot;:&quot;x_values&quot;},&quot;y&quot;:{&quot;field&quot;:&quot;y_values&quot;}},&quot;id&quot;:&quot;1060&quot;,&quot;type&quot;:&quot;Circle&quot;},{&quot;attributes&quot;:{&quot;end&quot;:1,&quot;format&quot;:&quot;0[.]00&quot;,&quot;js_property_callbacks&quot;:{&quot;change:value&quot;:[{&quot;id&quot;:&quot;1076&quot;},{&quot;id&quot;:&quot;1078&quot;}]},&quot;start&quot;:0,&quot;step&quot;:0.01,&quot;title&quot;:&quot;Sensitivity of test&quot;,&quot;value&quot;:1},&quot;id&quot;:&quot;1068&quot;,&quot;type&quot;:&quot;Slider&quot;},{&quot;attributes&quot;:{&quot;active_drag&quot;:&quot;auto&quot;,&quot;active_inspect&quot;:&quot;auto&quot;,&quot;active_multi&quot;:null,&quot;active_scroll&quot;:&quot;auto&quot;,&quot;active_tap&quot;:&quot;auto&quot;,&quot;tools&quot;:[{&quot;id&quot;:&quot;1024&quot;},{&quot;id&quot;:&quot;1025&quot;},{&quot;id&quot;:&quot;1026&quot;},{&quot;id&quot;:&quot;1027&quot;},{&quot;id&quot;:&quot;1028&quot;},{&quot;id&quot;:&quot;1029&quot;}]},&quot;id&quot;:&quot;1031&quot;,&quot;type&quot;:&quot;Toolbar&quot;},{&quot;attributes&quot;:{&quot;data&quot;:{&quot;x_values&quot;:[0,1,2,3,4,5,6,7,8,9,0,1,2,3,4,5,6,7,8,9,0,1,2,3,4,5,6,7,8,9,0,1,2,3,4,5,6,7,8,9,0,1,2,3,4,5,6,7,8,9],&quot;y_values&quot;:[0,0,0,0,0,0,0,0,0,0,1,1,1,1,1,1,1,1,1,1,2,2,2,2,2,2,2,2,2,2,3,3,3,3,3,3,3,3,3,3,4,4,4,4,4,4,4,4,4,4]},&quot;selected&quot;:{&quot;id&quot;:&quot;1093&quot;},&quot;selection_policy&quot;:{&quot;id&quot;:&quot;1094&quot;}},&quot;id&quot;:&quot;1003&quot;,&quot;type&quot;:&quot;ColumnDataSource&quot;},{&quot;attributes&quot;:{&quot;text&quot;:&quot;Positives = 50 \\t Negatives = 50 &quot;},&quot;id&quot;:&quot;1071&quot;,&quot;type&quot;:&quot;PreText&quot;},{&quot;attributes&quot;:{&quot;fill_alpha&quot;:{&quot;value&quot;:0.1},&quot;fill_color&quot;:{&quot;value&quot;:&quot;lightgreen&quot;},&quot;line_alpha&quot;:{&quot;value&quot;:0.1},&quot;line_color&quot;:{&quot;value&quot;:&quot;lightgreen&quot;},&quot;radius&quot;:{&quot;units&quot;:&quot;data&quot;,&quot;value&quot;:0.45},&quot;x&quot;:{&quot;field&quot;:&quot;x_values&quot;},&quot;y&quot;:{&quot;field&quot;:&quot;y_values&quot;}},&quot;id&quot;:&quot;1040&quot;,&quot;type&quot;:&quot;Circle&quot;},{&quot;attributes&quot;:{},&quot;id&quot;:&quot;1085&quot;,&quot;type&quot;:&quot;BasicTickFormatter&quot;},{&quot;attributes&quot;:{&quot;source&quot;:{&quot;id&quot;:&quot;1005&quot;}},&quot;id&quot;:&quot;1067&quot;,&quot;type&quot;:&quot;CDSView&quot;},{&quot;attributes&quot;:{&quot;fill_alpha&quot;:{&quot;value&quot;:0.1},&quot;fill_color&quot;:{&quot;value&quot;:&quot;black&quot;},&quot;line_alpha&quot;:{&quot;value&quot;:0.1},&quot;radius&quot;:{&quot;units&quot;:&quot;data&quot;,&quot;value&quot;:0.1},&quot;x&quot;:{&quot;field&quot;:&quot;x_values&quot;},&quot;y&quot;:{&quot;field&quot;:&quot;y_values&quot;}},&quot;id&quot;:&quot;1065&quot;,&quot;type&quot;:&quot;Circle&quot;},{&quot;attributes&quot;:{&quot;end&quot;:1,&quot;format&quot;:&quot;0[.]00&quot;,&quot;js_property_callbacks&quot;:{&quot;change:value&quot;:[{&quot;id&quot;:&quot;1077&quot;},{&quot;id&quot;:&quot;1078&quot;}]},&quot;start&quot;:0,&quot;step&quot;:0.01,&quot;title&quot;:&quot;Specificity of test&quot;,&quot;value&quot;:1},&quot;id&quot;:&quot;1069&quot;,&quot;type&quot;:&quot;Slider&quot;},{&quot;attributes&quot;:{&quot;text&quot;:&quot;&quot;},&quot;id&quot;:&quot;1083&quot;,&quot;type&quot;:&quot;Title&quot;},{&quot;attributes&quot;:{&quot;args&quot;:{&quot;slider_specificity&quot;:{&quot;id&quot;:&quot;1069&quot;},&quot;source_false_positives&quot;:{&quot;id&quot;:&quot;1006&quot;},&quot;source_negative&quot;:{&quot;id&quot;:&quot;1002&quot;},&quot;source_true_negatives&quot;:{&quot;id&quot;:&quot;1005&quot;}},&quot;code&quot;:&quot;\\n    var neg_data = source_negative.data;\\n    var test_pos_data = source_false_positives.data;\\n    var test_neg_data = source_true_negatives.data;\\n    var specificity = slider_specificity.value;\\n    test_pos_data[&#x27;x_values&#x27;]=[];\\n    test_pos_data[&#x27;y_values&#x27;]=[];\\n    test_neg_data[&#x27;x_values&#x27;]=[];\\n    test_neg_data[&#x27;y_values&#x27;]=[];\\n    var num_true_negatives = Math.floor(neg_data[&#x27;x_values&#x27;].length * specificity);\\n    var num_negatives = neg_data[&#x27;x_values&#x27;].length;\\n    var num_false_positives = num_negatives-num_true_negatives;\\n    var idx;\\n    var row=9;\\n    var col=9;\\n    for (idx=0;idx&lt;num_negatives; idx++){\\n        if (idx&lt;num_false_positives) {\\n            test_pos_data[&#x27;x_values&#x27;].push(col)\\n            test_pos_data[&#x27;y_values&#x27;].push(row)\\n        } else {\\n            test_neg_data[&#x27;x_values&#x27;].push(col)\\n            test_neg_data[&#x27;y_values&#x27;].push(row)\\n        }\\n        col = col - 1;\\n        if (col == -1) {\\n            col = 9\\n            row = row-1\\n        }\\n    }\\n    source_false_positives.change.emit();\\n    source_true_negatives.change.emit();\\n&quot;},&quot;id&quot;:&quot;1077&quot;,&quot;type&quot;:&quot;CustomJS&quot;},{&quot;attributes&quot;:{&quot;end&quot;:100,&quot;format&quot;:&quot;0[.]00&quot;,&quot;js_property_callbacks&quot;:{&quot;change:value&quot;:[{&quot;id&quot;:&quot;1075&quot;},{&quot;id&quot;:&quot;1076&quot;},{&quot;id&quot;:&quot;1077&quot;},{&quot;id&quot;:&quot;1078&quot;}]},&quot;start&quot;:0,&quot;title&quot;:&quot;Percentage of infected population&quot;,&quot;value&quot;:50},&quot;id&quot;:&quot;1070&quot;,&quot;type&quot;:&quot;Slider&quot;}],&quot;root_ids&quot;:[&quot;1081&quot;]},&quot;title&quot;:&quot;Bokeh Application&quot;,&quot;version&quot;:&quot;2.0.1&quot;}}';
                var render_items = [{"docid":"d3092d28-506f-4eaf-bcf8-db53a2d12ab0","root_ids":["1081"],"roots":{"1081":"1bfd456e-75ab-4994-a593-ac52950c63dd"}}];
                root.Bokeh.embed.embed_items(docs_json, render_items);
              
                }
                if (root.Bokeh !== undefined) {
                  embed_document(root);
                } else {
                  var attempts = 0;
                  var timer = setInterval(function(root) {
                    if (root.Bokeh !== undefined) {
                      clearInterval(timer);
                      embed_document(root);
                    } else {
                      attempts++;
                      if (attempts > 100) {
                        clearInterval(timer);
                        console.log("Bokeh: ERROR: Unable to run BokehJS code because BokehJS library is missing");
                      }
                    }
                  }, 10, root)
                }
              })(window);
            });
          };
          if (document.readyState != "loading") fn();
          else document.addEventListener("DOMContentLoaded", fn);
        })();

</script>

<div class="bk-root" id="1bfd456e-75ab-4994-a593-ac52950c63dd" data-root-id="1081"></div>
{{< /rawhtml >}}




## References:



[1] - Alfageme, A. (2020). Los test rapidos de coronavirus comprados en china no funcionan bien." https://elpais.com/sociedad/2020-03-25/los-test-rapidos-de-coronavirus-comprados-en-china-no-funcionan.html

[2] - Lalkhen, A. G., & McCluskey, A. (2008). Clinical tests: sensitivity and specificity. Continuing Education in Anaesthesia Critical Care \& Pain, 8(6),221–223. http://dx.doi.org/10.1093/bjaceaccp/mkn041

[3] - Loong, T. (2003). Understanding sensitivity and specificity with the right side of the brain. BMJ : British Medical Journal, 327(7417), 716–719

[4]  - Altman, D. G., & Bland, J. M. (1994). Diagnostic tests 2: Predictive values. BMJ : British Medical Journal, 309(6947), 102.

</Section>

