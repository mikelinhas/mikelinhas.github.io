---
layout: post
title: Process Analytics with Plotly and Docker
description: Portable Docker service that creates custom charts for different process readings. 
techstack: [Python, Flask, Docker, Plotly, VueJS]
---

---

## Introduction

Some of our PLCs were configured to generate data and save it into a `.csv` file on the client's Operating System. This system was usually **Windows** but could be any version of it. We needed a webapp which could chart the data in a user friendly way, and could be run in any OS. This is why we decided to run the web server inside a **Docker** container.

The webapp itself consisted of a small **VueJS** app for the frontend and a **Flask** backend which would import the python **Plotly** module for chart creation.

<p class="center" style="margin-top: 60px"> The main structure of the project was </p>

<div class="center-div" style="max-width: 700px; margin-top: 20px; margin-bottom: 50px;">
	<img src="/assets/images/Plotly-Docker.png" alt="Plotly Docker">
</div>


---

## Project Outline

### VueJS Frontend

##### VueJS Configuration

Using VueJS together with Flask's Jinja2 template engine required modifying VueJS configuration to use `[` brackets instead of the default `{` which are used by Jinja. This is done by setting the `delimiters` inside the VueJS app:

```javascript
var app = new Vue({
  el: '#app',
	
  delimiters: ["[[", "]]"],
	
  data: { ... },

  methods: { ... }
})
```

##### User interface
	
Users needed to select which CSV they wanted to chart, and which variables they wanted to see. The color and line type could also be configured in this step.

<div class="center-div" style="max-width: 700px; margin-top: 20px;">
	<img src="/assets/examples/csv-read-example.png" alt="CSV Read Example">
</div>

---

### Flask Backend

##### Creating Charts with Plotly

Once the csv and the variables were selected, Flask would run the plotly module and create a unique `graph.html` file. After building the chart, the user would be redirected and it would be loaded using the **Jinja2** engine.

```python

@app.route("/buildGraph", methods=["GET", "POST"])
def buildGraph():
  if request.method == "POST":

    filename = "graph.html"
    variables = request.form.getlist('variables')

    # Creates a chart with Plotly and saves it with the filename
    plotted_graph = plotly.graph(csv, variables, filename)
    
    return redirect(url_for('plot', filename=filename))


@app.route("/plot")
def plot():
	return render_template("plot.html", graph=graph)

```


<br />

<p class="center" style="margin-top: 60px"> An example of the graph: </p>


<div class="center-div" style="max-width: 900px; margin-top: 20px;">
	<img src="/assets/examples/graph-example.png" alt="Geaph example">
</div>


---

### Docker

##### Building the image

The image can be built from python with the following Dockerfile.

```java
FROM python:3.6.5-slim

LABEL maintainer="Miguel Zarco"

WORKDIR /app

ADD . /app

RUN pip install --upgrade pip

RUN pip install -r requirements.txt

CMD ["gunicorn", "--bind", "0.0.0.0:5000", "app:app"]
```

##### Running the image with an external volume

Once the image is created, we need to tell it where the `csv` files are located. This is done by attaching an external volume. Note that the `csv_folder` directory should exist in the app itself, in order for docker to use it as a mountpoint.

```bash
docker run -p 5000:5000 -v /path/to/csv_folder:/app/csv_folder app
```



---

## Example Demo

Checkout the live demo at <a href="http://graphup.herokuapp.com/" target="_blank"><img class="inline-image" src="/assets/Heroku.png" alt="Heroku"></a>



