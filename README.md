# CelebTrees
Trees of celebrities on twitter
<!DOCTYPE html>
<meta charset="utf-8">
<style>

.node circle {
  fill: #999;
}

.node text {
  font: 12px sans-serif;
}

.node--internal circle {
  fill: #555;
}

.node--internal text {
  text-shadow: 0 1px 0 #fff, 0 -1px 0 #fff, 1px 0 0 #fff, -1px 0 0 #fff;
}

.link {
  fill: none;
  stroke: #555;
  stroke-opacity: 0.4;
  stroke-width: 1.5px;
}

</style>
<svg width="2400" height="2000"></svg>
<script src="//d3js.org/d3.v4.min.js"></script>
<script>

var svg = d3.select("svg"),
    width = +svg.attr("width"),
    height = +svg.attr("height"),
    g = svg.append("g");

var cluster = d3.cluster()
    .size([width - 60, height - 60]);

var stratify = d3.stratify()
    .id(function(d) { return d.name; })
    .parentId(function(d) { return d.parent; });

d3.csv("Lil_YachtyColorTest.csv", function(error, data) {
  if (error) throw error;

  var root = stratify(data)
      .sort(function(a, b) { return (a.height - b.height) || a.id.localeCompare(b.id); });

  cluster(root);

  var link = g.selectAll(".link")
      .data(root.descendants().slice(1))
    .enter().append("path")
      .attr("class", "link")
      .attr("d", function(d) {
        return "M" + d.x + "," + d.y
            + "C" + d.x + "," + (d.parent.y + 100)
            + " " + d.parent.x + "," + (d.parent.y + 100)
            + " " + d.parent.x + "," + d.parent.y;
      });

  var node = g.selectAll(".node")
      .data(root.descendants())
    .enter().append("g")
      .attr("class", function(d) { return "node" + (d.children ? " node--internal" : " node--leaf"); })
      .attr("transform", function(d) { return "translate(" + d.x + "," + d.y + ")"; })

  node.append("circle")
      .attr("r", 5);

  node.append("text")
      .attr("x", 3)
      .attr("dy", function(d) { return d.children ? -10 : 20; })
      .style("text-anchor", function(d) { return d.children ? "end" : "start"; })
      .text(function(d) { return d.data.name; });
});

</script>
