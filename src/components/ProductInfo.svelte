<script  lang="ts">
 export {}
 import {onMount,onDestroy} from 'svelte';
 import { Datatable, rows } from 'svelte-simple-datatables';
 import Button from 'sveltestrap/src/Button.svelte';
 import Icon from 'fa-svelte';
 import { faDownload } from '@fortawesome/free-solid-svg-icons/faDownload';

interface DataTS {
ACOS: String,
TACOS: String,
accountId: String,
adConversions: Number,
adSales: any,
allOrderedUnits: Number,
allSales: any,
asin: String,
asin_descriptions: String,
clicks: Number,
cost: any,
currency: String,
date: String,
id: Number,
img:String,
impressions: Number,
marketplace: String,
      
  }

      let DataForTable=[];
      let API_RESPONSE_DATA:Array<number|string>=[];
      let SyncTableData=[];
  //Reset data bucket
  const InitDataBucket = () => {
       DataForTable=[];
       API_RESPONSE_DATA=[];
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
  DX.forEach((element:DataTS) => {
        //Compute Data For Table
        element.id=i++;
        element.asin='SIGNALYTICS' + i;
        element.asin_descriptions='Vivo Smartphone Android 10 8GB Ram 64GB Storage';
        element.img="https://dummyimage.com/60x60/f9f9fc/ff0059&text=Signalytic";
        element.ACOS=((element.cost / element.adSales) * 100).toFixed(2)+'%';
        element.TACOS= ((element.cost / element.allSales) * 100).toFixed(2)+'%';
        DataForTable.push(element);

      });
  SyncTableData=DataForTable;
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
          columnFilter: false,
          rowPerPage: 5,
          sortable: true,
          //scrollY: true,
          scrollX: true,
          css: true,
          labels: {
          search: 'Search by asin or title',    // search input placeholer
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
      };

  //Load init fucntion
  //Plot Graph When DOM Loaded
  onMount(async ()=>{
    await FetchDataAPI(dataurl);
  });
  onDestroy(()=>{
   // FetchDataAPI(dataurl);
  });
  let row;
  $:row={img:''};
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
      <th data-key="asin_descriptions">Product</th> 
        <th data-key="allSales">All Sales</th>
       <th data-key="allOrderedUnits">All Ordered Units</th>
        <th data-key="adSales">Ad Sales</th>
        <th data-key="adConversions">Ad Conversions</th>
        <th data-key="cost">Cost</th>
        <th data-key="clicks">Ad Clicks</th>
        <th data-key="impressions">Impressions</th>
        <th data-key="ACOS">ACOS</th>
        <th data-key="TACOS">Total ACOS</th> 
    </thead>
    <tbody>
        {#each $rows as row}
        <tr>
            <td>
              <div class='asinBox'>
                <span class='asinBoxImg'><img src={row['img']} style='height:60px;width:60px;' alt='Signalytics'></span>
                <span class='asinBoxDes'>{row['asin']}<br>{row['asin_descriptions']}</span>
                </div>
            </td> 
            <td>${row['allSales']}</td>
            <td>{row['allOrderedUnits']}</td>
            <td>${row['adSales']}</td>
            <td>{row['adConversions']}</td>
            <td>${row['cost']}</td>
            <td>{row['clicks']}</td>
            <td>{row['impressions']}</td>
            <td>{row['ACOS']}</td>
            <td>{row['TACOS']}</td> 
        </tr>
        {/each}
    </tbody>
  </Datatable>




<style type="text/css">

.asinBox{min-width: 300px;float: left;display: flex;}
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
  }
  :global(.dt-search>input){
    line-height: 34px !important;
    height: 35px !important;
    width: 350px !important;
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

</style>