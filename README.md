const routes: Routes = [
  {
    path: 'demo', // <-- keep this if you want /demo
    loadChildren: () =>
      import('@proj/trade-order-request').then(mod => mod.SampleModule)
      // ^ use your exact alias; module class must be SampleModule
  },
  { path: '', redirectTo: 'demo', pathMatch: 'full' }
];