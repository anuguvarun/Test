{
  path: 'trade-order-entry',
  canActivate: [
    {
      provide: OrderStepsGuard,
      useFactory: (accountService: AccountService, contextService: GuardContextService) => {
        const account = accountService.getActiveAccountSync(); // must be synchronous
        contextService.setContext(account);
        return new OrderStepsGuard(contextService, inject(Router));
      },
      deps: [AccountService, GuardContextService]
    }
  ],
  component: OrderStepsContainer,
}