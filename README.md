beforeEach(async () => {
  await TestBed.configureTestingModule({
    declarations: [TestHostComponent, RequestTradeComponent],
    providers: [
      { provide: Router, useValue: routerStub },
      { provide: RequestTradePresenter, useValue: presenter }, // adjust if you use a token
    ],
  }).compileComponents();

  fixture = TestBed.createComponent(TestHostComponent);
  debugEl = fixture.debugElement.query(By.directive(RequestTradeComponent));
  component = debugEl.componentInstance as RequestTradeComponent;

  component.headerForm = new FormGroup({
    brokerageAccount: new FormControl('', Validators.required),
  });

  // Mock scrollTo and scrollY without Object.defineProperty
  window.scrollTo = jest.fn();
  // If scrollY is used, just assign it — JSDOM makes it writable by default in tests
  (window as any).scrollY = 50;

  jest.useFakeTimers();
  jest.clearAllMocks();
});