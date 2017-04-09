# Chapter 5 with challenges

Code from chapter 5 of the Wilken book with challenges

index.html

```

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="initial-scale=1, maximum-scale=1, user-scalable=no, width=device-width">
    <title></title>

    <link href="lib/ionic/css/ionic.css" rel="stylesheet">
    <link href="css/style.css" rel="stylesheet">

    <!-- IF using Sass (run gulp sass first), then uncomment below and remove the CSS includes above
    <link href="css/ionic.app.css" rel="stylesheet">
    -->

    <!-- ionic/angularjs js -->
    <script src="lib/ionic/js/ionic.bundle.js"></script>
    <script src="lib/highcharts-release/adapters/standalone-framework.js"></script>
    <script src="lib/highcharts-release/highcharts.js"></script>
    <script src="lib/highcharts-ng/dist/highcharts-ng.js"></script>

    <!-- cordova script (this will be a 404 during development) -->
    <script src="cordova.js"></script>

    <!-- your app's js -->
    <script src="js/app.js"></script>
    <script src="views/detail/detail.js"></script>
    <script src="views/currencies/currencies.js"></script>
    <script src="views/history/history.js"></script>
    <script src="views/rates/rates.js"></script>
  </head>
  <body ng-app="App">
    <ion-nav-bar class="bar-positive">
      <ion-nav-back-button class="button-clear">
        <i class="ion-chevron-left"></i> Back
      </ion-nav-back-button>
    </ion-nav-bar>
    <ion-nav-view></ion-nav-view>
  </body>
</html>



```

app.js

```

angular.module('App', ['ionic', 'highcharts-ng'])

.config(function ($stateProvider, $urlRouterProvider) {

  $stateProvider
    .state('tabs', {
      url: '/tabs',
      abstract: true,
      templateUrl: 'views/tabs/tabs.html'
    })
    .state('tabs.rates', {
      url: '/rates',
      views: {
        'rates-tab': {
          templateUrl: 'views/rates/rates.html',
          controller: 'RatesController'
        }
      }
    })
    .state('tabs.history', {
      url: '/history?currency',
      views: {
        'history-tab': {
          templateUrl: 'views/history/history.html',
          controller: 'HistoryController'
        }
      }
    })
    .state('tabs.currencies', {
      url: '/currencies',
      views: {
        'currencies-tab': {
          templateUrl: 'views/currencies/currencies.html',
          controller: 'CurrenciesController'
        }
      }
    })
    .state('tabs.detail', {
      url: '/detail/:currency',
      views: {
        'rates-tab': {
          templateUrl: 'views/detail/detail.html',
          controller: 'DetailController'
        }
      }
    });

  $urlRouterProvider.otherwise('/tabs/rates');
})

.run(function($ionicPlatform) {
  $ionicPlatform.ready(function() {
    if(window.cordova && window.cordova.plugins.Keyboard) {
      cordova.plugins.Keyboard.hideKeyboardAccessoryBar(true);
    }
    if(window.StatusBar) {
      StatusBar.styleDefault();
    }
  });
})

.factory('Currencies', function () {
  return [
    { code: 'AUD', text: 'Australian Dollar', selected: true },
    { code: 'BRL', text: 'Brazilian Real', selected: false },
    { code: 'CAD', text: 'Canadian Dollar', selected: true },
    { code: 'CHF', text: 'Swiss Franc', selected: false },
    { code: 'CNY', text: 'Chinese Yuan', selected: true},
    { code: 'EUR', text: 'Euro', selected: true },
    { code: 'GBP', text: 'British Pound Sterling', selected: true },
    { code: 'IDR', text: 'Indonesian Rupiah', selected: false },
    { code: 'ILS', text: 'Israeli New Sheqel', selected: false },
    { code: 'MXN', text: 'Mexican Peso', selected: true },
    { code: 'NOK', text: 'Norwegian Krone', selected: false },
    { code: 'NZD', text: 'New Zealand Dollar', selected: false },
    { code: 'PLN', text: 'Polish Zloty', selected: false },
    { code: 'RON', text: 'Romanian Leu', selected: false },
    { code: 'RUB', text: 'Russian Ruble', selected: true },
    { code: 'SEK', text: 'Swedish Krona', selected: false },
    { code: 'SGD', text: 'Singapore Dollar', selected: false },
    { code: 'USD', text: 'United States Dollar', selected: true },
    { code: 'ZAR', text: 'South African Rand', selected: false }
  ];
});



```

currencies.html

```

<ion-view view-title="Currencies">
  <ion-nav-buttons side="primary">
    <button class="button" ng-click="state.reordering = !state.reordering">Reorder</button>
  </ion-nav-buttons>
  <ion-content>
    <ion-list show-reorder="state.reordering">
      <ion-item class="item-toggle" ng-repeat="currency in currencies">
        {{currency.code}} - {{currency.text}}
        <label class="toggle toggle-balanced">
        <input type="checkbox" ng-model="currency.selected">
          <div class="track">
            <div class="handle"></div>
          </div>
        </label>
        <ion-reorder-button class="ion-navicon" on-reorder="move(currency, $fromIndex, $toIndex)"></ion-reorder-button>
      </ion-item>
    </ion-list>
  </ion-content>
</ion-view>



```

currencies.js

```

angular.module('App')
.controller('CurrenciesController', function ($scope, Currencies) {
  $scope.currencies = Currencies;
  $scope.state = {
    reordering: false
  };

  $scope.$on('$stateChangeStart', function () {
    $scope.state.reordering = false;
  });

  $scope.move = function(currency, fromIndex, toIndex) {
    $scope.currencies.splice(fromIndex, 1);
    $scope.currencies.splice(toIndex, 0, currency);
  };
});



```

detail.html

```

<ion-view view-title="Detail for {{currency.code}}">
  <ion-content>
    <ion-list>
      <ion-item>Last <span class="badge badge-stable">{{currency.ticker.last}}</span></ion-item>
      <ion-item>Ask <span class="badge badge-balanced">{{currency.ticker.ask}}</span></ion-item>
      <ion-item>Bid <span class="badge badge-assertive">{{currency.ticker.bid}}</span></ion-item>
      <ion-item>24h Average <span class="badge badge-dark">{{currency.ticker['24h_avg']}}</span></ion-item>
      <ion-item>24h Volume <span class="badge badge-stable icon ion-social-bitcoin"> {{currency.ticker.total_vol | number:2}}</span></ion-item>
      <ion-item ui-sref="tabs.history({currency: currency.code})" class="item-icon-right">View History <span class="icon ion-arrow-right-b"></span></ion-item>
    </ion-list>
  </ion-content>
  <ion-footer-bar class="bar-dark">
    <h1 class="title">Updated {{currency.ticker.timestamp | date:'mediumTime'}}</h1>
  </ion-footer-bar>
</ion-view>



```

detail.js

```

angular.module('App')
.controller('DetailController', function ($scope, $stateParams, $state, Currencies) {

  angular.forEach(Currencies, function (currency) {
    if (currency.code === $stateParams.currency) {
      $scope.currency = currency;
    }
  });

  if (angular.isUndefined($scope.currency.ticker)) {
    $state.go('tabs.rates');
  }

});



```

history.html

```

<ion-view view-title="Hourly Average Price" hide-back-button="true">
  <ion-content>
    <div class="list list-inset">
      <label class="item item-input item-select">
        <div class="input-label">
          Currency
        </div>
        <select ng-change="changeCurrency()" ng-model="history.currency">
          <option ng-repeat="currency in currencies | filter:{selected:true}" value="{{currency.code}}" ng-selected="history.currency == currency.code">{{currency.code}} - {{currency.text}}</option>
        </select>
      </label>
    </div>
    <highchart config="chart"></highchart>
  </ion-content>
</ion-view>


```

history.js

```

angular.module('App')
.controller('HistoryController', function ($scope, $http, $state, $stateParams, Currencies) {

  $scope.history = {
    currency: $stateParams.currency || 'USD'
  };
  $scope.currencies = Currencies;

  $scope.changeCurrency = function () {
    $state.go('tabs.history', { currency: $scope.history.currency });
  };

  $scope.chart = {
    options: {
      chart: {
        type: 'line'
      },
      legend: {
        enabled: false
      }
    },
    title: {
      text: null
    },
    yAxis: {
      title: null
    },
    xAxis: {
      type: 'datetime'
    },
    series: []
  };

  $http.get('https://api.bitcoinaverage.com/history/' + $scope.history.currency + '/per_hour_monthly_sliding_window.csv').success(function (prices) {

    prices = prices.split(/\n/);
    var series = {
      data: []
    };

    angular.forEach(prices, function (price, index) {
      price = price.split(',');
      var date = new Date(price[0].replace(' ', 'T')).getTime();
      var value = parseFloat(price[3]);
      if (date && value > 0) {
        series.data.push([date, value]);
      }
    });

    $scope.chart.series.push(series);
  });

  $scope.$on('$ionicView.beforeEnter', function() {
    $scope.history = {
      currency: $stateParams.currency || 'USD'
    };
  });
});



```

help-popover.html

```

<ion-popover-view>
  <ion-header-bar>
    <h1 class="title">About Bitcoin</h1>
  </ion-header-bar>
  <ion-content>
    <div class="padding">This shows the last bitcoin transaction price for a currency and compares it to the 24 hour rolling average rate.</div>
    <div class="padding">Data is available up to once a minute.</div>
    <div class="padding">The data for this application is from the <a href="https://bitcoinaverage.com/api">Bitcoin Average</a> API.</div>
  </ion-content>
</ion-popover-view>


```

rates.html

```

<ion-view view-title="Current Rates" hide-back-button="true">
  <ion-nav-buttons side="primary">
    <button class="button" ng-click="openHelp($event)">About</button>
  </ion-nav-buttons>
  <ion-content>
    <ion-refresher on-refresh="load()" pulling-text="Pull to Refresh"></ion-refresher>
    <ion-list>
      <ion-item ng-repeat="currency in rc.currencies | filter:{selected:true}" ui-sref="tabs.detail({currency: currency.code})">
        {{currency.code}} - {{currency.text}}
        <span class="price" ng-if="currency.ticker.last == currency.ticker['24h_avg']">
          {{currency.ticker.last || '0.00'}}<br />0.00
        </span>
        <span class="price negative" ng-if="currency.ticker.last < currency.ticker['24h_avg']">
          {{currency.ticker.last}}<br /><span class="icon ion-arrow-down-b"></span> {{currency.ticker['24h_avg'] - currency.ticker.last | number:2}}
        </span>
        <span class="price positive" ng-if="currency.ticker.last > currency.ticker['24h_avg']">
          {{currency.ticker.last}}<br /><span class="icon ion-arrow-up-b"></span> {{currency.ticker.last - currency.ticker['24h_avg'] | number:2}}
        </span>
      </ion-item>
    </ion-list>
  </ion-content>
  <ion-footer-bar class="bar-dark">
    <h1 class="title">Updated {{rc.currencies[0].ticker.timestamp | date:'mediumTime'}}</h1>
  </ion-footer-bar>
</ion-view>


```

rates.js

```

angular.module('App')
.controller('RatesController', function ($scope, $http, $ionicPopover, Currencies) {
  
  $scope.currencies = Currencies;

  $ionicPopover.fromTemplateUrl('views/rates/help-popover.html', {
    scope: $scope,
  }).then(function (popover) {
    $scope.popover = popover;
  });
  $scope.openHelp = function($event) {
    $scope.popover.show($event);
  };
  $scope.$on('$destroy', function() {
    $scope.popover.remove();
  });

  $scope.load = function () {
    $http.get('https://api.bitcoinaverage.com/ticker/all').success(function (tickers) {
      angular.forEach($scope.currencies, function (currency) {
        currency.ticker = tickers[currency.code];
        currency.ticker.timestamp = new Date(currency.ticker.timestamp);
      });
    }).finally(function () {
      $scope.$broadcast('scroll.refreshComplete');
    });
  };
});



```

tabs.html

```

<ion-tabs class="tabs-icon-top tabs-positive">

  <ion-tab title="Rates" icon-on="ion-social-bitcoin" icon-off="ion-social-bitcoin-outline" ui-sref="tabs.rates">
    <ion-nav-view name="rates-tab"></ion-nav-view>
  </ion-tab>

  <ion-tab title="History" icon-on="ion-ios-analytics" icon-off="ion-ios-analytics-outline" ui-sref="tabs.history">
    <ion-nav-view name="history-tab"></ion-nav-view>
  </ion-tab>

  <ion-tab title="Currencies" icon-on="ion-ios-cog" icon-off="ion-ios-cog-outline" ui-sref="tabs.currencies">
    <ion-nav-view name="currencies-tab"></ion-nav-view>
  </ion-tab>

</ion-tabs>



```