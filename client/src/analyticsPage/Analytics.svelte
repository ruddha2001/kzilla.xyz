<script>
  import { onMount } from "svelte";
  import BrowserBox from "./BrowserBox.svelte";
  import SmallBox from "./SmallBox.svelte";
  import UpperRow from "./UpperRow.svelte";
  import HeroTag from "./HeroTag.svelte";
  import Navbar from "../components/NavbarStructure.svelte";
  import Footer from "../components/Footer.svelte";
  import { getAnalyticsData } from "../services/APIservice.js";

  let button_content = "All Time";
  var data;
  let ddToggler = false;
  var dateObj = new Date();
  var today = new Date();
  dateObj.setDate(dateObj.getDate());
  today.setDate(today.getDate());
  var startdate = "";
  var enddate = "";
  var analyticsId = window.location.href.split("analytics/")[1];

  //   Initialize onMount
  onMount(async function initialise() {
    data = await getAnalyticsData(analyticsId, startdate, enddate);
  });
  // Changed Start Date using Custom
  var changeStartDate = async event => {
    startdate = event.detail.startdate;
    data = null;
    data = await getAnalyticsData(analyticsId, startdate, enddate);
  };
  // Change End Date using Custom
  var changeEndDate = async event => {
    data = null;
    enddate = event.detail.enddate;
    data = await getAnalyticsData(analyticsId, startdate, enddate);
  };
  //Changed Dates Range
  var datesChanged = async value => {
    button_content = value;
    ddToggler = false;
    if (value != "custom" && value != "allTime") {
      today = new Date();
      today.setDate(today.getDate());
      if (value == "today") {
        dateObj.setDate(today.getDate());
        dateObj.setMonth(today.getMonth());
      } else if (value == "yesterday") {
        dateObj.setDate(today.getDate() - 1);
        dateObj.setMonth(today.getMonth());
      } else if (value == "past3Days") {
        dateObj.setDate(today.getDate() - 2);
        dateObj.setMonth(today.getMonth());
      } else if (value == "thisMonth") {
        dateObj.setMonth(today.getMonth());
        dateObj.setDate(today.getDate() - 30);
      } else if (value == "thisWeek") {
        dateObj.setDate(today.getDate() - 6);
        dateObj.setMonth(today.getMonth());
      }
      enddate = formatDate(today);
      startdate = formatDate(dateObj);
    }
    if (value == "allTime") {
      startdate = "";
      enddate = "";
    }
    data = null;
    data = await getAnalyticsData(analyticsId, startdate, enddate);
  };
  // Format the date
  function formatDate(date) {
    var d = new Date(date),
      month = "" + (d.getMonth() + 1),
      day = "" + d.getDate(),
      year = d.getFullYear();

    if (month.length < 2) month = "0" + month;
    if (day.length < 2) day = "0" + day;

    return [year, month, day].join("-");
  }

  function toggleDropDown() {
    ddToggler = !ddToggler;
  }
</script>

<style>
  .super-container {
    position: relative;
    min-height: 96vh;
  }
  .footer-container {
    position: absolute;
    bottom: -20px;
    width: 100%;
  }
  .no-data {
    font-family: UniSansBook;
    font-size: 20px;
  }
  .error-message {
    margin-top: 170px;
  }
  .kz-dropdown {
    position: fixed;
    right: 3.4vw;
    top: 14.4vh;
    width: 11.6vw;
    border-radius: 16px;
    background-color: #f5f5f5;
    border: none;
    padding-top: 8px;
    z-index: 999;
  }
  .kz-dropdown ul {
    list-style-type: none;
    font-family: UniSansHeavy;
    margin-left: -40px;
  }
  .kz-dropdown ul li {
    list-style-type: none;
    padding: 2px 0px;
    padding-left: 20px;
  }
  .kz-dropdown ul li:hover {
    background-color: var(--black);
    color: var(--white);
  }
  @media (max-width: 1400px) {
    .kz-dropdown {
      width: 12.5vw;
    }
  }
  @media (max-width: 1200px) {
    .kz-dropdown {
      width: 14.5vw;
    }
  }
  @media (max-width: 1024px) {
    .kz-dropdown {
      width: 17.5vw;
    }
  }
  /* Copy */
  .header {
    margin-top: 4vh;
    padding-left: 8vw;
    padding-right: 8vw;
  }

  @media (max-width: 920px) {
    .header {
      padding-left: 5vw;
      padding-right: 5vw;
    }
    .kz-dropdown {
      right: 2.5vw;
      top: 14.4vh;
      width: 15.5vw;
    }
  }
  @media (max-width: 500px) {
    .kz-dropdown {
      right: 2.2vw;
      top: 14.4vh;
      width: 30vw;
    }
  }
  @media (max-width: 760px) {
    .kz-dropdown {
      right: 2.4vw;
      top: 14.4vh;
      width: 20vw;
    }
    .no-data {
      font-size: 14px;
      padding: 16px 20px;
    }
  }
  @media (max-width: 600px) {
    .kz-dropdown {
      right: 2.4vw;
      top: 14.4vh;
      width: 23vw;
    }
  }
  @media (max-width: 500px) {
    .kz-dropdown {
      right: 2.4vw;
      top: 14.4vh;
      width: 35vw;
    }
  }
</style>

<div class="super-container">
  <Navbar
    on:changeStartDate={changeStartDate}
    on:changeEndDate={changeEndDate}
    on:dropDown={toggleDropDown}
    {button_content} />
  {#if ddToggler}
    <div class="kz-dropdown">
      <ul>
        <li role="button" on:click={() => datesChanged('allTime')}>All Time</li>
        <li role="button" on:click={() => datesChanged('today')}>Today</li>
        <li role="button" on:click={() => datesChanged('past3Days')}>
          Last 3 days
        </li>
        <li role="button" on:click={() => datesChanged('thisWeek')}>
          This Week
        </li>
        <li role="button" on:click={() => datesChanged('thisMonth')}>
          This Month
        </li>
        <li role="button" on:click={() => datesChanged('yesterday')}>
          Yesterday
        </li>
        <li role="button" on:click={() => datesChanged('custom')}>Custom</li>
      </ul>
    </div>
  {/if}
  {#if data}
    {#if data.status === 429}
      <p class="text-center no-data error-message">
        Our systems believe you are on to something bad so they have temporarily
        blocked you. Please try again in a while.
      </p>
    {:else if data.status === 400}
      <p class="text-center no-data error-message">
        We went to the moon and back, but could not find any more data.
      </p>
    {:else if data.status === 404}
      <p class="text-center no-data error-message">
        Something has gone wrong. The link is broken, or the world is ending.
        Either way, we're investigating the cause.
      </p>
    {:else}
      <div class="header">
        <HeroTag {analyticsId} />
        <UpperRow {data} />
        <div class="row">
          <div class="col-md-6">
            <SmallBox analyticsData={data.reports[0]} heading={'sources'} />
          </div>
          <div class="col-md-6">
            <SmallBox analyticsData={data.reports[1]} heading={'city'} />
          </div>
        </div>
        <BrowserBox {data} />
        {#if data.reports.length <= 0}
          <div class="text-center no-data">
            We went to the moon and back, but could not find any more data.
          </div>
        {/if}
      </div>
    {/if}
  {:else}
    <p class="text-center no-data error-message">
      Computing the secret to life, the universe, and everything...
    </p>
  {/if}
  <div class="footer-container">
    <Footer />
  </div>

</div>
