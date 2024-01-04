<script setup>
import { onMounted, ref } from 'vue'
import * as go from 'gojs'

const props = defineProps({ nodeDataArray: Array, linkDataArray: Array })
const emitter = defineEmits([
  "ExternalObjectsDropped",
  "SelectionMoved"
])

const diagram = ref(null)

onMounted(function() {
  const $ = go.GraphObject.make;

  const myDiagram =
    new go.Diagram(diagram.value,
      {
        "undoManager.isEnabled": true
      });
  
  myDiagram.addDiagramListener("ExternalObjectsDropped", e => emitter("ExternalObjectsDropped", e))
  myDiagram.addDiagramListener("SelectionMoved", e => emitter("SelectionMoved", e))

  myDiagram.nodeTemplate =
    $(go.Node, "Auto",
      $(go.Shape,
        { fill: "white" },
        new go.Binding("fill", "color")),
      $(go.TextBlock,
        { margin: 8 },
        new go.Binding("text"))
    );

  myDiagram.linkTemplate =
    $(go.Link,
      {
        relinkableFrom: true, relinkableTo: true,
        reshapable: true, resegmentable: true
      },
      $(go.Shape),
      $(go.Shape, { toArrow: "OpenTriangle" })
    );

  const nda = props.nodeDataArray;
  const lda = props.linkDataArray;
  myDiagram.model = new go.GraphLinksModel(nda, lda);
})
</script>

<template>
  <div ref="diagram" class="goDiagram"></div>
</template>

<style scoped>
.goDiagram {
  width: 400px;
  height: 400px;
  border: solid black 1px;
}
</style>
