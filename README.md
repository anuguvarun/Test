describe('RequestTradeSuccessComponent', () => {
  let component: RequestTradeSuccessComponent;

  beforeEach(() => {
    jest.clearAllMocks();
    component = new RequestTradeSuccessComponent(
      orderStepPresenterMock,
      analyticsServiceMock
    );
  });

  test('setting cardValues triggers analytics purchase event once', () => {
    const cards = [
      fg({ id: 1, actionToggle: ActionType.Buy }),
      fg({ id: 2, actionToggle: ActionType.Sell }),
    ];

    component.cardValues = cards;

    expect(analyticsServiceMock.trackEvent).toHaveBeenCalledTimes(1);
    // Check the payload shape without coupling to PurchaseEvent implementation:
    expect(analyticsServiceMock.trackEvent).toHaveBeenCalledWith(
      expect.objectContaining({
        type: 'DonorFlex Trade Card',
        products: expect.arrayContaining([
          expect.objectContaining({ index: 0 }),
          expect.objectContaining({ index: 1 }),
        ]),
      })
    );
  });

  test('ngOnChanges splits cards into buyCards and sellCards', () => {
    const buy1 = fg({ id: 'b1', actionToggle: ActionType.Buy });
    const buy2 = fg({ id: 'b2', actionToggle: ActionType.Buy });
    const sell1 = fg({ id: 's1', actionToggle: ActionType.Sell });

    component.cardValues = [buy1, sell1, buy2];

    // Act
    component.ngOnChanges();

    expect(component.buyCards.map(c => c.value.id)).toEqual(['b1', 'b2']);
    expect(component.sellCards.map(c => c.value.id)).toEqual(['s1']);
  });

  test('ngOnInit forwards cardValues to orderStepPresenter.updateTotalsByAction', () => {
    const cards = [
      fg({ id: 10, actionToggle: ActionType.Buy }),
      fg({ id: 11, actionToggle: ActionType.Sell }),
    ];
    component.cardValues = cards;
    orderStepPresenterMock.updateTotalsByAction.mockClear(); // isolate ngOnInit call

    component.ngOnInit();

    expect(orderStepPresenterMock.updateTotalsByAction).toHaveBeenCalledTimes(1);
    expect(orderStepPresenterMock.updateTotalsByAction).toHaveBeenCalledWith(
      cards
    );
  });

  test('ngOnChanges is a no-op if cardValues is undefined', () => {
    component.cardValues = undefined;
    component.buyCards = ['placeholder'];
    component.sellCards = ['placeholder'];

    component.ngOnChanges();

    expect(component.buyCards).toEqual(['placeholder']);
    expect(component.sellCards).toEqual(['placeholder']);