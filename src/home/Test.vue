/**
* Created by dgunzi on 2018/7/26.
 */

<template>
  <s-index id="app">
    <div id="topology_graph" :style="{height: topologyBodyHeight + 'px'}">
        <div id="topologyMenu" style="margin-top:10px;margin-bottom:10px;">
            <!-- <s-button class="collapse_btn" title="移动" @click="pan">移动</s-button>
            <s-button class="collapse_btn" title="居中" @click="center">居中</s-button>
            <s-button class="collapse_btn" title="移动" @click="fit">铺满整张图</s-button>
            <s-button class="collapse_btn" title="移动" @click="mouseChange">抓屏/点击</s-button>
             <s-button class="collapse_btn" title="移动" @click="viewXML">查看当前图的xml</s-button> -->
        </div>
        <div id="graph_canvas" :style="{height: topologyGraphHeight + 'px'}" style="background-color: #ffc">
        </div>
        <div  class="dragsource">
            <img src="/static/images/topologyicon/default/DNS.svg" id="dns" width="42px" height="42px">
        </div>
        <div  class="dragsource" style="left: 90px">
            <img src="/static/images/topologyicon/default/Firewall.svg" id="firewall" width="42px" height="42px">
        </div>
        <!-- <div class="gmnoprint graph_map_overview">
            <div class="outline grpah_outline"></div>
        </div> -->
    </div>
    <!-- <div class="control_btns">
        <s-button icon="reduce" @click="zoomOut" title="缩小"></s-button>
        <s-button icon="home" @click="resetZoom" title="重置"></s-button>
        <s-button icon="enlarge" @click="zoomIn" title="放大"></s-button>
    </div> -->
  </s-index>
</template>
<script type="text/babel">
import Grail from '@/components/grailLayout/Grail'
  export default {
    data () {
      return {
        topologyBodyHeight: 600,
        topologyGraphHeight: 500,
        graph: null,
        panning: false
      }
    },
    components: {
      's-index': Grail
    },
    methods: {
      center() {
        this.graph.center();
      },
      fit() {
        this.graph.fit();
      },
      pan() {
        // var translate = this.graph.view.getTranslate();
        // this.graph.view.setTranslate(translate.x, translate.y + 120);
        var translate = this.graph.view.getTranslate();
        this.graph.view.setTranslate(translate.x + 120, translate.y);
      },
      mouseChange() {
        this.panning = !this.panning;
        this.graph.panningHandler.useLeftButtonForPanning = this.panning;
        this.graph.panningHandler.setPanningEnabled(this.panning);
      },
      viewXML() {
        let encoder = new window.mxCodec();
        let node = encoder.encode(this.graph.getModel());
        console.log(window.mxUtils.getPrettyXml(node));
        window.alert(window.mxUtils.getPrettyXml(node));
    },
      zoomOut() {
        this.graph.zoomOut();
      },
      zoomIn() {
        this.graph.zoomIn();
      },
      resetZoom() {
        this.graph.zoomActual();
      },
      initDivHeight () {
        var footerHeight = document.querySelector('.s-footer').offsetHeight;
        var headerHeight = document.getElementById('headBar').offsetHeight;
        var menuHeight = document.getElementById('topologyMenu').offsetHeight;
        this.topologyBodyHeight = window.innerHeight - headerHeight - footerHeight - 38;
        this.topologyGraphHeight = this.topologyBodyHeight - menuHeight;
      },
      initGraph() {
        if (!window.mxClient.isBrowserSupported()) {
            window.mxUtils.error('Browser is not supported!', 200, false);
        } else {
            let container = document.getElementById('graph_canvas')
            this.graph = new window.mxGraph(container);

            // 各种配置，对graph进行限制和控制

            // 设置不允许节点改变大小
            this.graph.setCellsResizable(false);
            // 设置是否两点可连多线
            this.graph.setMultigraph(false);
            // 设置不允许线悬空
            this.graph.setAllowDanglingEdges(false);
            // this.addStyle();                   // 普通方法加入样式
            // this.loadXmlStyle();            // 载入一个样式配置文件
            this.graph.setHtmlLabels(true); // 给cell变个身，html使用起来更容易
            this.loadData();
            // this.addOutLine();
        }
      },
      addStyle() {                // 添加样式，可以mxCell长得漂亮些
        var style = this.graph.getStylesheet().getDefaultVertexStyle();
        style[window.mxConstants.STYLE_SHAPE] = window.mxConstants.SHAPE_ELLIPSE;
        style[window.mxConstants.STYLE_PERIMETER] = window.mxPerimeter.EllipsePerimeter;
        style[window.mxConstants.STYLE_GRADIENTCOLOR] = 'white';
        style[window.mxConstants.STYLE_FONTSIZE] = '10';
      },
      loadXmlStyle() {            // 添加样式配置文件
        var node = window.mxUtils.load(window.mxBasePath + '/style.xml').getDocumentElement();
        var dec = new window.mxCodec(node.ownerDocument);
        dec.decode(node, this.graph.getStylesheet());
      },
      addOutLine() {               // 鹰眼（缩略图）
        var div = document.querySelector('.grpah_outline');
        this.outline = new window.mxOutline(this.graph, div);
        this.outline.setZoomEnabled(false);
      },
      loadData() {
            let graph = this.graph;
            graph.getModel().beginUpdate();
            var layout = new window.mxFastOrganicLayout(graph);
            layout.forceConstant = 80;
            let w = 30;
            let h = 30;
            let parent = graph.getDefaultParent();
            try {
                // var vertexStyle = 'shape=cylinder;strokeWidth=2;fillColor=#ffffff;strokeColor=black;' + 'gradientColor=#a0a0a0;fontColor=black;fontStyle=1;spacingTop=14;'; // 直接给某个cell加样式
                var v1 = graph.insertVertex(parent, null, 'A', 0, 0, w, h);
                var v2 = graph.insertVertex(parent, null, 'B', 0, 0, w, h);
                var v3 = graph.insertVertex(parent, null, 'C', 0, 0, w, h);
                var v4 = graph.insertVertex(parent, null, 'D', 0, 0, w, h);
                var v5 = graph.insertVertex(parent, null, 'E', 0, 0, w, h);
                var v6 = graph.insertVertex(parent, null, 'F', 0, 0, w, h);
                var v7 = graph.insertVertex(parent, null, 'G', 0, 0, w, h);
                var v8 = graph.insertVertex(parent, null, 'H', 0, 0, w, h);
                // graph.insertVertex(parent, null, '<div class="cellStyle">Sunflower</div>', 500, 300, 90, 90);
                graph.insertEdge(parent, null, 'ab', v1, v2);
                graph.insertEdge(parent, null, 'ac', v1, v3);
                graph.insertEdge(parent, null, 'cd', v3, v4);
                graph.insertEdge(parent, null, 'be', v2, v5);
                graph.insertEdge(parent, null, 'cf', v3, v6);
                graph.insertEdge(parent, null, 'ag', v1, v7);
                graph.insertEdge(parent, null, 'gh', v7, v8);
                graph.insertEdge(parent, null, 'gc', v7, v3);
                graph.insertEdge(parent, null, 'gd', v7, v4);
                graph.insertEdge(parent, null, 'eh', v5, v8);
                layout.execute(parent);
            } finally {
                graph.getModel().endUpdate();
            }
      },
      createDrag(id) {
            let img = document.getElementById(id);
            let graph = this.graph;
            let funct = function(graph, evt, cell, x, y) { // 拖拽完成后回调函数
                if (graph.canImportCell(cell)) {
                    var parent = graph.getDefaultParent();
                    var vertex = null;
                    graph.getModel().beginUpdate();
                    try {
                        vertex = graph.insertVertex(parent, null, id, x, y, 42, 42);
                    } finally {
                        graph.getModel().endUpdate();
                    }
                    graph.setSelectionCell(vertex);
                }
            }
            var dragImage = img.cloneNode(true);                     // 拖拽进行时鼠标下的元素
            dragImage.style.width = '32px';
            dragImage.style.height = '32px';
            window.mxUtils.makeDraggable(img, graph, funct, dragImage);     // 绑定事件
        }
    },
    mounted() {
        this.initDivHeight();
        this.initGraph();
        window.addEventListener('resize', this.initDivHeight);
        this.createDrag('dns')
        this.createDrag('firewall')
    },
    destroyed () {
      window.removeEventListener('resize', this.initDivHeight);
      window.mxEvent.removeAllListeners(window);
      window.mxEvent.removeAllListeners(document);
    }
  }
</script>
<style lang="stylus" rel="stylesheet/stylus">
    .cellStyle{
        background-color:blueviolet;
        height: 90px;
        width: 90px;
        color: cornsilk;
        line-height: 90px;
        font-size: 20px;
    }
    .dragsource
        width  42px
        height 42px
        border 1px solid #ccc
        position absolute
        left 30px
        bottom 30px
        background-color #ff00ff
    .gmnoprint
        z-index 99
        background-color #fff
        position absolute
        right 0px
        bottom 0px
        width 160px
        height 160px
        .outline
            margin-left 3px
            margin-top 3px
            overflow hidden
            width 156px
            height 156px
            position absolute
            border-left 1px solid #cccccc
            border-top 1px solid #cccccc
    .control_btns
        position absolute 
        right 10px
        top 50px         
</style>