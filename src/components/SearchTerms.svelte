<script  lang="ts">
  export {}
    import {onMount,onDestroy} from 'svelte';
    import { Datatable, rows } from 'svelte-simple-datatables';
    import Button from 'sveltestrap/src/Button.svelte';
    import Icon from 'fa-svelte';
    import { faDownload } from '@fortawesome/free-solid-svg-icons/faDownload';

    let TotalCalc: {
    total_impressions: number;
    total_clicks: number;
    total_adConversions: number;
    total_adSales: number;
    total_cost: number;
    total_acos: number | string;
    total_cr: number | string;
    total_ctr: number | string;
}

let TotalCalcFiltred: {
    Ftotal_impressions: number;
    Ftotal_clicks: number;
    Ftotal_adConversions: number;
    Ftotal_adSales: number;
    Ftotal_cost: number;
    Ftotal_acos: number | string;
    Ftotal_cr: number | string;
    Ftotal_ctr: number | string;
}
   
      let DataForTable=[];
      let API_RESPONSE_DATA=[];
      let SyncTableData=[];
  
      TotalCalc={total_impressions:0,total_clicks:0,total_adConversions:0,total_adSales:0,total_cost:0,total_acos:0,total_cr:0,total_ctr:0};
      TotalCalcFiltred={Ftotal_impressions:0,Ftotal_clicks:0,Ftotal_adConversions:0,Ftotal_adSales:0,Ftotal_cost:0,Ftotal_acos:0,Ftotal_cr:0,Ftotal_ctr:0};
  //Data computation code
  
  
  //Reset data bucket
  const InitDataBucket = () => {
       DataForTable=[];
       API_RESPONSE_DATA=[];
       TotalCalc={total_impressions:0,total_clicks:0,total_adConversions:0,total_adSales:0,total_cost:0,total_acos:0,total_cr:0,total_ctr:0};
       TotalCalcFiltred={Ftotal_impressions:0,Ftotal_clicks:0,Ftotal_adConversions:0,Ftotal_adSales:0,Ftotal_cost:0,Ftotal_acos:0,Ftotal_cr:0,Ftotal_ctr:0};
  }
  
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
    let i=1;
  DX.forEach(element => {
        //Compute Data For Table
        element.id=i++;
        element.asin='SIGNALYTICS' + i;
        element.asin_descriptions='Vivo Smartphone Android 10 8GB Ram 64GB Storage';
        element.img="https://dummyimage.com/60x60/f9f9fc/ff0059&text=Signalytic";
        element.searchTerm='AB' + i;
        element.ACOS=((element.cost / element.adSales) * 100).toFixed(2)+'%';
        element.cr= ((element.adConversions / element.clicks) * 100).toFixed(2)+'%';
        element.ctr= ((element.clicks / element.impressions) * 100).toFixed(2)+'%';
        DataForTable.push(element);
    
       TotalCalc.total_impressions+=Number(element.impressions);
       TotalCalc.total_clicks+=Number(element.clicks);
       TotalCalc.total_adConversions+=Number(element.adConversions);
       TotalCalc.total_adSales+=Number(element.adSales.toFixed(0));
       TotalCalc.total_cost+=Number(element.cost.toFixed(0));
    });
  SyncTableData=DataForTable;
TotalCalc.total_acos=((Number(TotalCalc.total_cost)/Number(TotalCalc.total_adSales)) * 100).toFixed(2)+'%';
TotalCalc.total_cr=((Number(TotalCalc.total_adConversions)/Number(TotalCalc.total_clicks)) * 100).toFixed(2)+'%';
TotalCalc.total_ctr=((Number(TotalCalc.total_clicks)/Number(TotalCalc.total_impressions)) * 100).toFixed(2)+'%';
  }
  
  
    //Custom Data Filter Via API Urls 
    const options = [
    { FilterFlag: 'Daily',DataURL:'https://api.npoint.io/802712efe490308ca615'},
    { FilterFlag: 'Weekly',DataURL:'https://api.npoint.io/68af5a18c668961011c5'},
    { FilterFlag: 'Monthly',DataURL:'https://api.npoint.io/1998e1765b7a482d6fc6'},
  ];
  
  let selected = options[0];
  
  //Data Table Setting
  const settings = { 
    columnFilter: true,
          rowPerPage: 5,
          sortable: true,
          scrollY: true,
          scrollX: true,
          css: true,
          labels: {
          search: 'Search by asin or asin description',    // search input placeholer
          filter: 'Filter',       // filter inputs placeholder
          noRows: 'No entries to found',
          info: 'Showing {start} to {end} of {rows} entries',
          previous: 'Previous',
          next: 'Next',       
      },
      blocks: {
          searchInput: true, 
          paginationButtons: true,
          paginationRowCount: true,
      }
      }
  
  //Load init fucntion
  //Plot Graph When DOM Loaded
  onMount(async ()=>{
    await FetchDataAPI(dataurl);
    });
  onDestroy(()=>{
   // FetchDataAPI(dataurl);
  });

  //Reset Filtered data bucket
  $:TotalCalcFiltred;
 const CalcFilterdData = (DataStack) =>{
  TotalCalcFiltred={Ftotal_impressions:0,Ftotal_clicks:0,Ftotal_adConversions:0,Ftotal_adSales:0,Ftotal_cost:0,Ftotal_acos:0,Ftotal_cr:0,Ftotal_ctr:0};
   if(DataStack.length>0)
   {
    DataStack.forEach(DX=>{
    TotalCalcFiltred.Ftotal_impressions+=Number(DX.impressions.toFixed(0));
    TotalCalcFiltred.Ftotal_clicks+=Number(DX.clicks.toFixed(0));
    TotalCalcFiltred.Ftotal_adConversions+=Number(DX.adConversions.toFixed(0));
    TotalCalcFiltred.Ftotal_adSales+=Number(DX.adSales.toFixed(0));
    TotalCalcFiltred.Ftotal_cost+=Number(DX.cost.toFixed(0));
   });
   //Calc Percentages
   TotalCalcFiltred.Ftotal_acos=((TotalCalcFiltred.Ftotal_cost/TotalCalcFiltred.Ftotal_adSales) * 100).toFixed(2)+'%';
   TotalCalcFiltred.Ftotal_cr=((TotalCalcFiltred.Ftotal_adConversions/TotalCalcFiltred.Ftotal_clicks) * 100).toFixed(2)+'%';
   TotalCalcFiltred.Ftotal_ctr=((TotalCalcFiltred.Ftotal_clicks/TotalCalcFiltred.Ftotal_impressions) * 100).toFixed(2)+'%';

//Append Temp Data for Table
let TempData={
ACOS:TotalCalcFiltred.Ftotal_acos,
accountId: "",
adConversions:TotalCalcFiltred.Ftotal_adConversions,
adSales:TotalCalcFiltred.Ftotal_adSales,
allOrderedUnits: 0,
allSales: 0,
asin: "Total Filtered",
asin_descriptions: "",
clicks: TotalCalcFiltred.Ftotal_clicks,
cost: TotalCalcFiltred.Ftotal_cost,
cr: TotalCalcFiltred.Ftotal_cr,
ctr: TotalCalcFiltred.Ftotal_ctr,
currency: "",
date: "", 
id: 0 ,
img: "",
impressions: TotalCalcFiltred.Ftotal_impressions,
marketplace: "",
searchTerm: ""
};
$rows.push(TempData);
//Total Data
let TotalTData={
ACOS:TotalCalc.total_acos,
accountId: "",
adConversions:TotalCalc.total_adConversions,
adSales:TotalCalc.total_adSales,
allOrderedUnits: 0,
allSales: 0,
asin: "Total",
asin_descriptions: "",
clicks: TotalCalc.total_clicks,
cost: TotalCalc.total_cost,
cr: TotalCalc.total_cr,
ctr: TotalCalc.total_ctr,
currency: "",
date: "", 
id: 0 ,
img: "",
impressions: TotalCalc.total_impressions,
marketplace: "",
searchTerm: ""
};
$rows.push(TotalTData);
  }
}

  $:CalcFilterdData($rows); 
  </script>

  <!-- Custom filters -->
  <span id="CustomFilterBox">
    <!-- Filter By :--> 
    <select bind:value={selected} on:blur|preventDefault={()=>FetchDataAPI(selected.DataURL)} id="CustomFilterBoxOptions">
      {#each options as option}
        <option value={option}>{option.FilterFlag}</option>
      {/each}
    </select>
  </span>
  <span class="DownloadTableData">
    <Button class="btn" size="sm">
      <Icon icon={faDownload}></Icon>
     </Button>
  </span>
  <!-- Print Data Into Table Based On Filtered Range-->
  
  <Datatable settings={settings} data={SyncTableData}>
    <thead> 
        <th data-key="asin_descriptions">ASIN</th>
        <th data-key="searchTerm">Search Term</th>
        <th data-key="impressions">Impressions</th>
        <th data-key="clicks">Clicks</th>
        <th data-key="adConversions">Conversions</th>
        <th data-key="adSales">Sales</th>
        <th data-key="cost">Cost</th>  
        <th data-key="ACOS">ACOS</th>
        <th data-key="cr">CR</th>
        <th data-key="ctr">CTR</th>
    </thead>
    <tbody>
  
        {#each $rows as row}
        {#if row['id']!=0}
        <tr>
            <td>
                <div class='asinBox'>
                    <span class='asinBoxImg'><img src={row['img']} style='height:60px;width:60px;' alt='Signalytics'></span>
                    <span class='asinBoxDes'>{row['asin']}<br>{row['asin_descriptions']}</span>
                </div>
             </td>
            <td>{row['searchTerm']}</td>
            <td>{row['impressions']}</td>
            <td>{row['clicks']}</td>
            <td>{row['adConversions']}</td>
            <td>${row['adSales']}</td>
            <td>${row['cost']}</td>
            <td>{row['ACOS']}</td>
            <td>{row['cr']}</td>
            <td>{row['ctr']}</td>
        </tr>
        {:else}
        <tr class='FooterFixedRows'>
          <td>
              <div class='TabFooterBox'>
              <span class='totalHead'>{row['asin']}</span>
              </div>
           </td>
           <td>{row['searchTerm']}</td>
           <td>{row['impressions']}</td>
           <td>{row['clicks']}</td>
           <td>{row['adConversions']}</td>
           <td>${row['adSales']}</td>
           <td>${row['cost']}</td>
           <td>{row['ACOS']}</td>
           <td>{row['cr']}</td>
           <td>{row['ctr']}</td>
      </tr>
        {/if}
        {/each}
    </tbody>
  </Datatable>

  
<style>
  .asinBox{
        min-width: 300px;
        float: left;
        display: flex;
      }
      .asinBoxDes{
        text-align: justify;
       margin-left: 5px;
      }

    :global(.SearchTermFooter table th) {
      text-align:center;padding:4px 8px;white-space:nowrap;
      color:#727477 !important; 
      font-weight:400 !important; 
    }  
    :global(.SearchTermFooter th:first-child){width:56px;}
    
  th:first-child{width:56px;}
  td{text-align:center;padding:4px 8px;white-space:nowrap;}

   th,tr,td{
       font-size: 12px;
   } 
   td{text-align:center;padding:4px 0} 
   :global(section.datatable){
     height:auto !important;
   }
   :global(article.dt-table){
     height:auto !important;
     overflow-x: scroll !important;
   }
  
  :global(.applyButton){
  width: 100px !important;
    height: 32px !important;
    border: none !important;
  }
  #CustomFilterBox{
    display: inline-block;
    margin-bottom: 10px;
  } 
  #CustomFilterBoxOptions{
  height: 25px;
  border: none;
  box-shadow: 2px 2px 4px 2px #d6d0d0;
}
 
  :global(.datatable tr:nth-child(even)){background-color: #f2f2f2;}
  :global(.datatable)
  {
    height: auto !important;
  }
  :global(.dt-search)
  {
    margin-left: 0px !important;
    height:auto !important;
    display: flex !important;
    float: left !important;
  } 
  :global(.dt-search>input){
    line-height: 34px !important;
    height: 35px !important;
    width: 350px !important;
    margin-bottom: 15px !important;
  }
  :global(.dt-table)
  {
    margin-top: 10px !important;
  }
  :global(.dt-header th){
      background-color: rgb(248, 249, 252) !important;
      border-bottom: none !important;
      padding: 2px 0px 2px 4px !important;
      color:rgb(249, 44, 81) !important;
  }

  :global(th.filter)
  {
    padding: 2px 0px 2px 2px !important;
    margin: 0 !important;
    border: none !important;
    background: white !important;
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

.DownloadTableData{
  float: right;
    color:  rgb(249, 44, 81) !important;
    background: #ffffff;
    box-shadow: 2px 2px 4px 2px #d6d0d0;
}

:global(.DownloadTableData button){
  color: red;
    border: none;
    background: #f8f9fc;
}

:global(.FooterFixedRows){
  color: black;
  font-weight: 500;
}

:global(.FooterFixedRows td){
  font-size: 15px !important;
}

</style>