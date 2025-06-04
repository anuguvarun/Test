describe('TradeOrderSuccessComponent', () => {
  let component: TradeOrderSuccessComponent;
  let fixture: ComponentFixture<TradeOrderSuccessComponent>;
  let mockOrderStepPresenter: any;

  beforeEach(() => {
    mockOrderStepPresenter = {
      getCardInfoByAction: jest.fn()
    };

    TestBed.configureTestingModule({
      declarations: [TradeOrderSuccessComponent],
      providers: [
        { provide: OrderStepsPresenter, useValue: mockOrderStepPresenter }
      ]
    });

    fixture = TestBed.createComponent(TradeOrderSuccessComponent);
    component = fixture.componentInstance;

    // ✅ Mocked like FormGroup, but not using FormGroup to avoid .get() issues
    component['cardValues'] = [
      { value: { actionToggle: ActionType.Buy } },
      { value: { actionToggle: ActionType.Sell } },
      { value: { actionToggle: ActionType.Buy } }
    ] as any;

    fixture.detectChanges();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });

  it('should filter buy and sell cards correctly on ngOnInit', () => {
    component.ngOnInit();

    expect(component.buyCards.length).toBe(2);
    expect(component.sellCards.length).toBe(1);

    expect(component.buyCards).toEqual([
      { value: { actionToggle: ActionType.Buy } },
      { value: { actionToggle: ActionType.Buy } }
    ]);

    expect(component.sellCards).toEqual([
      { value: { actionToggle: ActionType.Sell } }
    ]);
  });

  it('should call getCardInfoByAction with cardValues', () => {
    component.ngOnInit();
    expect(mockOrderStepPresenter.getCardInfoByAction).toHaveBeenCalledWith(component['cardValues']);
  });
});