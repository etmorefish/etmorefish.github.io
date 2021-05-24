---
title: demo
top: false
cover: false
toc: true
mathjax: true
date: 2020-12-31 15:51:46
password:
summary:
tags:
categories:
---

```html
                <!-- i/o used begin-->
                <h4 class="pt-5">I/O</h4>
                <ul id="io_tabs" role="tablist" class="nav nav-tabs">
                    <li class="list-group list-group-horizontal">
                        <a id="io_tablink" data-toggle="tab" href="#read_tabpanel" role="tab"
                            aria-controls="read_tabpanel" class="nav-link active"> Raw Usage </a>
                        <a id="io_tablink" data-toggle="tab" href="#write_tabpanel" role="tab"
                            aria-controls="write_tabpanel" class="nav-link"> %Allocated </a>
                    </li>
                </ul>
                <div class="tab-content">
                    <div class="tab-pane active" id="read_tabpanel" role="tabpanel" style="height:500px; width: 100%;">
                    </div>
                    <div class="tab-pane" id="write_tabpanel" role="tabpanel" style="height:500px; width: 100%;">
                    </div>
                </div>
                <!-- i/o used end-->
```

```javascript
var myChart = echarts.init(document.getElementById('cpu_tabpanel'), 'light');

// 指定图表的配置项和数据
var option = {
    // 全局调色盘。
    // color: ['#2f4554', '#61a0a8', '#d48265', '#91c7ae','#749f83',  '#ca8622', '#bda29a','#6e7074', '#546570', '#c4ccd3'],
    title: {
        text: 'CPU Usage',
        left: "center"
    },
    tooltip: {},
    legend: {
        left: "12%",
        data: ['%cpu']
    },
    xAxis: {
        type: 'category',
        axisLabel: { interval: 0, rotate: -45 },
        data: []
    },
    yAxis: [{
        type: "value",
        show: true,
        position: "left",
        offset: 0,
        name: "% single core CPU useage",
        nameLocation: "middle",
        nameTextStyle: {
            color: "rgba(19, 113, 200, 1)",
            fontStyle: "normal",
            fontWeight: "bold",
            fontFamily: "sans-serif",
            fontSize: 13,
            align: "center",
            lineHeight: 50
        }
    }],
    series: [{
        name: '%cpu',
        type: 'bar',
        data: []
    }],
    toolbox: {
        show: true,
        feature: {
            dataZoom: {
                yAxisIndex: "none"
            },
            dataView: {
                readOnly: false
            },
            magicType: {
                type: ["line", "bar"]
            },
            restore: {},
            saveAsImage: {}
        }
    },
};

var test = window.location.href;
ids = test.split('/')
axios.get('/api/v1/workflow/' + ids[5], {
    params: {

    }
})
    .then(function (response) {
        for (i = 0, max = response.data.data.length; i < max; i++) {
            // console.log(response.data.data[i].cpu_precent);
            option.series[0].data.push(response.data.data[i].cpu_precent);
            option.xAxis.data.push(response.data.data[i].process);
        }
        // console.log(response.data.data.length);
        // console.log(option.series);
        // console.log(option.xAxis);
        myChart.setOption(option);

    })
    .catch(function (error) {
        console.log(error);
    });

// pctcpu_tabpanel
var myChart2 = echarts.init(document.getElementById('pctcpu_tabpanel'), 'light');

var option2 = {
    title: {
        text: '% Requested CPU Used',
        left: "center"
    },
    tooltip: {},
    legend: {
        left: "12%",
        data: ['%requested']
    },
    xAxis: {
        type: 'category',
        axisLabel: { interval: 0, rotate: -45 },
        data: []
    },
    yAxis: [{
        type: "value",
        show: true,
        position: "left",
        offset: 0,
        name: "% Allocated CPUs Used",
        nameLocation: "middle",
        nameTextStyle: {
            color: "rgba(19, 113, 200, 1)",
            fontStyle: "normal",
            fontWeight: "bold",
            fontFamily: "sans-serif",
            fontSize: 13,
            align: "center",
            lineHeight: 50
        }
    }],
    series: [{
        name: '%requested',
        type: 'bar',
        data: []
    }],
    toolbox: {
        show: true,
        feature: {
            dataZoom: {
                yAxisIndex: "none"
            },
            dataView: {
                readOnly: false
            },
            magicType: {
                type: ["line", "bar"]
            },
            restore: {},
            saveAsImage: {}
        }
    },
};

axios.get('/api/v1/workflow/' + ids[5], {
    params: {

    }
})
    .then(function (response) {
        for (i = 0, max = response.data.data.length; i < max; i++) {
            // console.log(response.data.data[i].cpu_precent);
            option2.series[0].data.push(response.data.data[i].cpus / 20);
            option2.xAxis.data.push(response.data.data[i].process);
        }
        // console.log(response.data.data.length);
        myChart2.setOption(option2);

    })
    .catch(function (error) {
        console.log(error);
    });

```

