<script lang="ts">
  //Load : Mount Dom & Destroy : Lifecycle Hooks
  import {onMount,onDestroy} from 'svelte';
  //Load the dependencies
  import Chart from "chart.js";
  import Button from 'sveltestrap/src/Button.svelte';
  import Icon from 'fa-svelte';
  import { faDownload } from '@fortawesome/free-solid-svg-icons/faDownload';


  let TotleCalc: {
    total_allSales: number;
    total_tacos:String|Number;
    total_sponsoredSales: number;
    total_cost: number;
    total_acos: String|Number;
    total_clicks: number;
    total_impressions: number;
}

//Default variables
    let DefaultBgcolor='#f8f9fc';
    let DefaultTextColor='#8a8c8e';  

    let graphClicks = [];
    let graphImpressions = [];
    let graphCost = [];
    let graphAdSale = [];
    let graphAllSale = [];
    let garphAcos=[];
    let garphTacos=[];
    let xAxisLabel=[];
    let API_RESPONSE_DATA=[];
    TotleCalc={total_allSales:0,total_tacos:0,total_sponsoredSales:0,total_cost:0,total_acos:0,total_clicks:0,total_impressions:0};

    

//Reset data bucket
const InitDataBucket = () => {
     graphClicks = [];
     graphImpressions = [];
     graphCost = [];
     graphAdSale = [];
     graphAllSale = [];
     garphAcos=[];
     garphTacos=[];

     xAxisLabel=[];
     API_RESPONSE_DATA=[];
     TotleCalc={total_allSales:0,total_tacos:0,total_sponsoredSales:0,total_cost:0,total_acos:0,total_clicks:0,total_impressions:0};
}

let myChart;

type GrphDataSets = {
    label: string;
    data: any[];
    yAxisID: string;
    borderWidth: number;
    borderColor: string;
    backgroundColor: string;
    fill: boolean;
    lineTension: number; 
}

$: AllSalesChartData={label:'All Sales',data:graphAllSale,yAxisID:'',borderWidth:2,borderColor:"#969696",backgroundColor:"#969696",fill:false,lineTension:0};
$: TacosChartData={label:'TACOS',data:garphTacos,yAxisID:'',borderWidth:2,borderColor:"#f0ad4e",backgroundColor:"#f0ad4e",fill:false,lineTension:0};
$: SponsoredSalesChartData ={label:'Sponsored Sales',data:graphAdSale,yAxisID:'y-axis-1',borderWidth:2,borderColor:"#ff2c54",backgroundColor:"#ff2c54",fill:false,lineTension:0};
$: CostChartData={label:'Cost',data:graphCost,yAxisID:'y-axis-2',borderWidth:2,borderColor:"#313a46e8",backgroundColor:"#313a46e8",fill:false,lineTension:0};
$: AcosChartData={label:'ACOS',data:garphAcos,yAxisID:'',borderWidth:2,borderColor:"#7b9fe8",backgroundColor:"#7b9fe8",fill:false,lineTension:0};
$: ClicksChartData={label:'Clicks',data:graphClicks,yAxisID:'',borderWidth:2,borderColor:"#8e6ba0",backgroundColor:"#8e6ba0",fill:false,lineTension:0};
$: ImpressionsChartData ={label:'Impressions',data:graphImpressions,yAxisID:'',borderWidth:2,borderColor:"#f9b1c7",backgroundColor:"#f9b1c7",fill:false,lineTension:0};

//By Default Daily Data  
const dataurl = 'https://api.npoint.io/802712efe490308ca615';
const  FetchDataAPI= async (API_URL) => {  
InitDataBucket();
const API_RESPONSE_RAW_DATA = await fetch(API_URL);
API_RESPONSE_DATA = await API_RESPONSE_RAW_DATA.json();
ComputeAPI_Data(API_RESPONSE_DATA);
} 
//Compute Data
const ComputeAPI_Data = (DX) => {
DX.forEach(element => {
      graphClicks.push(element.clicks);
      graphImpressions.push(element.impressions);
      graphCost.push(element.cost);
      graphAdSale.push(element.adSales);
      graphAllSale.push(element.allSales);
      garphAcos.push(((element.cost / element.adSales) * 100).toFixed(2));
      garphTacos.push(((element.cost / element.allSales) * 100).toFixed(2));
      xAxisLabel.push(element.date);
      //Compute Data For Total
      TotleCalc.total_allSales+=element.allSales;
      TotleCalc.total_cost+=element.cost;
      TotleCalc.total_sponsoredSales+=element.adSales;
      TotleCalc.total_clicks=element.clicks;
      TotleCalc.total_impressions=element.impressions;
    });

 //Make round figure   
 TotleCalc.total_allSales=Math.round(TotleCalc.total_allSales);
 TotleCalc.total_cost=Math.round(TotleCalc.total_cost);
 TotleCalc.total_sponsoredSales=Math.round(TotleCalc.total_sponsoredSales);
 //TotleCalc.total_clicks=Math.floor(TotleCalc.total_clicks);
 //TotleCalc.total_impressions=Math.floor(TotleCalc.total_impressions);
//Plot Chart 
SignalyticsPlotGraph();
TotleCalc.total_tacos=Math.round((TotleCalc.total_cost/TotleCalc.total_sponsoredSales) * 100)+'%';
TotleCalc.total_acos=Math.round((TotleCalc.total_cost/TotleCalc.total_allSales) * 100)+'%';
}

//Default Chart Options
  const chartOptions = {
  tooltips: {
					mode: 'index',
          footerFontStyle: 'normal',
          callbacks: {
				  label: function(tooltipItem, data) {
             //console.log(tooltipItem,data) ;
             return data.datasets[tooltipItem.datasetIndex].label+" : $"+tooltipItem.value;
            }
					}
				},
				hover: {
					mode: 'index',
					intersect: true
				},
    scales: {
      xAxes: [{
						display: true,
						scaleLabel: {
							show: false,
							labelString: 'Month'
						},
        gridLines: {
          display: false,
          lineWidth: 1
        }
					}],
						yAxes: [{
              type: 'linear',
              position:'left',
              id: 'y-axis-1',
              ticks: {
                    // Include a dollar sign in the ticks
                    callback: function(value, index, values) {
                        return '$' + value;
                    }
                }
						}, {
              type: 'linear',
              position:'right', 
              id: 'y-axis-2',
              ticks: {
                    // Include a dollar sign in the ticks
                    callback: function(value, index, values) {
                      return '$' + value;
                    }
                },
          //Hide grid line for right side y-axis      
          gridLines: {
          display: false,
          lineWidth: 1
        }
            }
          ]
					}
  };

//Plot Graph using chart js
  const SignalyticsPlotGraph:any=(ComputedGraphDATA:any)=>{
  if (myChart) {
        GraphStateManager();
       }
  else{
    const ctx:HTMLElement = document.getElementById("lineChart");
     myChart = new Chart(ctx, {
      type: "line",
      data: {
        //For The X Axis
        labels: xAxisLabel,
        //Default Chart Datasets
        datasets: [SponsoredSalesChartData,CostChartData]
      },
      options: chartOptions
    });
  }
 
  };

  //Update Graph
  let ActiveDivBoxQueue=['total_sponsoredSales','total_cost'];
  const UpdateLineGraph = (e:any=false,DataQueue:any=false) =>{
    let PrevDataQueue=myChart.data.datasets;
    if(e!=false)
    {
    //Filter Dublicate and Provide Toggle functionality
    if(PrevDataQueue.find(DQ=>DQ==DataQueue)!=undefined)
    {
      if(PrevDataQueue.length>1)
      {
      PrevDataQueue=PrevDataQueue.filter(DQ=>DQ!=DataQueue);
      ActiveDivBoxQueue=ActiveDivBoxQueue.filter(DM_ID=>DM_ID!=e.currentTarget.id);
      }
    }
    else
    {
      PrevDataQueue.push(DataQueue);
      ActiveDivBoxQueue.push(e.currentTarget.id);
      if(PrevDataQueue.length>2)
      {
        PrevDataQueue.shift();
        ActiveDivBoxQueue.shift();
      }
      
    }
   }
   else{
    //Y-Axis
    PrevDataQueue.forEach((DtQ,Index)=>{
      if(DtQ.label=='All Sales')
       {
        PrevDataQueue[Index]=AllSalesChartData;
       }
       else if(DtQ.label=='TACOS')
       {
        PrevDataQueue[Index]=TacosChartData;
       }
       else if(DtQ.label=='Sponsored Sales')
       {
        PrevDataQueue[Index]=SponsoredSalesChartData;
       }
       else if(DtQ.label=='Cost')
       {
        PrevDataQueue[Index]=CostChartData;
       }
       else if(DtQ.label=='ACOS')
       {
        PrevDataQueue[Index]=AcosChartData;
       }
       else if(DtQ.label=='Clicks')
       {
        PrevDataQueue[Index]=ClicksChartData;
       }
       else if(DtQ.label=='Impressions')
       {
        PrevDataQueue[Index]=ImpressionsChartData;
       }
    });

   }
    //Compute Axis Position
    let LeftSepratorY_One="";
    let RightSepratorY_One="";
    let LeftSepratorY_Two="";
    let RightSepratorY_Two=""; 

    //Manage Y-1 Axis Data
    if(PrevDataQueue.length>0)
    {
      PrevDataQueue[0].yAxisID='y-axis-1';
    if(PrevDataQueue[0].label=='All Sales' || PrevDataQueue[0].label=='Sponsored Sales' || PrevDataQueue[0].label=='Cost')
    {
      LeftSepratorY_One="$";
    }
    else if(PrevDataQueue[0].label=='TACOS' || PrevDataQueue[0].label=='ACOS')
    {
      RightSepratorY_One="%";
    }
    myChart.options = {
      scales: {
						yAxes: [{
              type: 'linear',
              position:'left',
              id:'y-axis-1',
              ticks: {
                     // Include sign before/After parameter
                    callback: function(value, index, values) {
                        return LeftSepratorY_One + value + RightSepratorY_One;
                    }
                }
						}
          ]
					}
    };
    }
    
    //Manage Y-2 Axis Data
    if(PrevDataQueue.length==2)
    {
    PrevDataQueue[1].yAxisID='y-axis-2';
    if(PrevDataQueue[1].label=='All Sales' || PrevDataQueue[1].label=='Sponsored Sales' || PrevDataQueue[1].label=='Cost'){
      LeftSepratorY_Two="$";
    }
    else if(PrevDataQueue[1].label=='TACOS' || PrevDataQueue[1].label=='ACOS')
    {
      RightSepratorY_Two="%";
    }
    let SecYAxis={
              type: 'linear',
              position:'right', 
              id: 'y-axis-2',
              ticks: {
                    // Include sign before/After parameter
                    callback: function(value, index, values) {
                      return LeftSepratorY_Two + value + RightSepratorY_Two;
                    }
                },
          //Hide grid line for right side y-axis      
          gridLines: {
          display: false,
          lineWidth: 1
        }
            };

            myChart.options.scales.yAxes.push(SecYAxis);
    }

    //Compute CSS for active and non-active blocks
     document.querySelectorAll('.data-statistics-ai').forEach(DIvs=>{
      let Dvx:any;
      Dvx=DIvs; 
      Dvx.style.background=DefaultBgcolor;
      Dvx.style.color=DefaultTextColor;
     });

    //Reset div with active style code
    let i=0;
    ActiveDivBoxQueue.forEach(divId=>{
      document.getElementById(divId).style.background=PrevDataQueue[i].backgroundColor;
      document.getElementById(divId).style.color='white';
      i++;
    });
    //X-Axis Config
    let xAxes=[{
						display: true,
						scaleLabel: {
							show: false,
							labelString: 'Month'
						},
        gridLines: {
          display: false,
          lineWidth: 1
        }
          }];
    myChart.options.scales.xAxes=xAxes;     
    //ReCOnfigure tooltip & hover mode
    let ToolTips= {
					mode: 'index',
          footerFontStyle: 'normal',
          callbacks: {
				  label: function(tooltipItem, data) {
    let DataSETLabel=data.datasets[tooltipItem.datasetIndex].label;
    if(DataSETLabel=='All Sales' || DataSETLabel=='Sponsored Sales' || DataSETLabel=='Cost')
    {
      tooltipItem.yLabel= DataSETLabel + ': $' + tooltipItem.yLabel;
    }
    else if(DataSETLabel=='TACOS' || DataSETLabel=='ACOS')
    {
      tooltipItem.yLabel= DataSETLabel + ':' + tooltipItem.yLabel + '%';
    }
    else 
    {
      tooltipItem.yLabel= DataSETLabel + ':' + tooltipItem.yLabel;
    }
            
     return tooltipItem.yLabel;
            }
					}
				};
        let hvr={
					mode: 'index',
					intersect: true
        };
    myChart.options.tooltips=ToolTips;    
    myChart.options.hover=hvr;   
         
    //Update Chart
    myChart.data.labels=xAxisLabel;
    myChart.data.datasets=PrevDataQueue;
    myChart.update();
  }
//Graph State Manager
const GraphStateManager=()=>{
UpdateLineGraph();
}

  //Custom Data Filter Via API Urls 
  const options = [
  { FilterFlag: 'Daily',DataURL:'https://api.npoint.io/802712efe490308ca615'},
  { FilterFlag: 'Weekly',DataURL:'https://api.npoint.io/68af5a18c668961011c5'},
  { FilterFlag: 'Monthly',DataURL:'https://api.npoint.io/1998e1765b7a482d6fc6'},
];

let selected = options[0];

//Plot Graph When DOM Loaded
onMount(async ()=>{
  await FetchDataAPI(dataurl);
});
onDestroy(()=>{
 // FetchDataAPI(dataurl); 
});


</script>
<div id="DataBarInfo">
  <div class="flex-container">
  <div class="data-statistics-ai" id='total_allSales' on:click="{(e)=>{UpdateLineGraph(e,AllSalesChartData)}}">
      <span class="gridTitle">ALL SALES</span><br>
    <span class="gridValue">${TotleCalc.total_allSales}</span>
    </div>
    <div class="data-statistics-ai" id='total_tacos' on:click="{(e)=>{UpdateLineGraph(e,TacosChartData)}}">
      <span class="gridTitle">TACOS</span><br>
      <span class="gridValue">{TotleCalc.total_tacos}</span>
    </div>
  <div class="data-statistics-ai" id='total_sponsoredSales' on:click="{(e)=>{UpdateLineGraph(e,SponsoredSalesChartData)}}" style="background:#ff2c54;color:white;">
      <span class="gridTitle">Sponsored Sales</span><br>
      <span class="gridValue">${TotleCalc.total_sponsoredSales}</span>
    </div>
    <div class="data-statistics-ai" id='total_cost' on:click="{(e)=>{UpdateLineGraph(e,CostChartData)}}" style="background:#313a46e8;color:white;"> 
      <span class="gridTitle">COST</span><br>
      <span class="gridValue">${TotleCalc.total_cost}</span>
    </div>
    <div class="data-statistics-ai" id='total_acos' on:click="{(e)=>{UpdateLineGraph(e,AcosChartData)}}">
      <span class="gridTitle">ACOS</span><br>
      <span class="gridValue">{TotleCalc.total_acos}</span>
    </div>
    <div class="data-statistics-ai" id='total_clicks' on:click="{(e)=>{UpdateLineGraph(e,ClicksChartData)}}">
      <span class="gridTitle">CLICKS</span><br>
      <span class="gridValue">{TotleCalc.total_clicks}</span>
    </div>
    <div class="data-statistics-ai" id='total_impressions' on:click="{(e)=>{UpdateLineGraph(e,ImpressionsChartData)}}">
      <span class="gridTitle">IMPRESSIONS</span><br>
      <span class="gridValue">{TotleCalc.total_impressions}</span>
    </div>
  </div>
</div>  
<!-- Date Range Selecter-->
<div id="Signalytics_DatePickerBOX">
<!-- Custom filters -->
<span id="CustomFilterBox">
  <!-- Filter By :--> 
  <select bind:value={selected} on:blur|preventDefault={()=>FetchDataAPI(selected.DataURL)} id="CustomFilterBoxOptions">
    {#each options as option}
      <option value={option}>{option.FilterFlag}</option>
    {/each}
  </select>
</span>
<span class="DownloadGraphData">
  <Button class="btn" size="sm">
   <Icon icon={faDownload}/>
  </Button>
</span>
</div>

<canvas id="lineChart" /> 



<style>
:global(.applyButton){
width: 100px !important;
  height: 32px !important;
  border: none !important;
}
#CustomFilterBox{
 
/*margin-left: 20px;
border-left: 2px solid #ff9f40;
height: 90px;
padding-left: 20px;
*/
padding-top: 10px;
display: inline-block;
}  

#CustomFilterBoxOptions{
  height: 25px;
  border: none;
  box-shadow: 2px 2px 4px 2px #d6d0d0;
}

.flex-container {
  display: flex;
  background-color:white;
}

.flex-container > div {
  background-color:#f8f9fc;
    color: #8a8c8e;
    width: 200px;
    padding: 5px;
    text-align: center;
    box-shadow: 0px 1px 3px 0px #8a8c8e;
}

.gridTitle{
  font-weight: 500;
    font-size: 13px;
}
.gridValue{
  font-size: 23px;
    font-weight: lighter;
}

:global(.card){
    position: relative;
    display: flex;
    flex-direction: column;
    min-width: 0px;
    overflow-wrap: break-word;
    background-color: rgb(255, 255, 255);
    background-clip: border-box;
    box-shadow: rgba(58, 59, 69, 0.15) 0px 0.15rem 1.75rem 0px !important;
    border-width: 1px;
    border-style: solid;
    border-color: rgb(227, 230, 240);
    border-image: initial;
    border-radius: 0.35rem;
}

:global(.card-header){
    margin-bottom: 0px;
    background-color: rgb(248, 249, 252);
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 16px 20px;
    border-bottom: 1px solid rgb(227, 230, 240);

}

:global(h3.card-title) {
    font-size: 16px;
    font-weight: 500;
    color: rgb(249, 44, 81);
    line-height: 1em;
    margin: 0px;
}

/*
Date Picker Class
*/

:root {
  --sliderStartBackgroundColor: rgb(249, 44, 81);
}

:global(.sliderStart,.sliderEnd){
margin-bottom: 20px !important;
background-color: var(--sliderStartBackgroundColor);
}
.DownloadGraphData{
  float: right;
    color:  rgb(249, 44, 81) !important;
    margin-top: 10px;
    background: #ffffff;
    box-shadow: 2px 2px 4px 2px #d6d0d0;
}

:global(.DownloadGraphData button){
  color: red;
    border: none;
    background: #f8f9fc;
}

:global(.applyButton){
  background-color: #f1f4fd !important;
    color: #ff2c54 !important;
}

</style>
