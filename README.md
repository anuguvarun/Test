// ActionType enum
enum ActionType {
  Buy = 'Buy',
  Sell = 'Sell'
}

// Stub of your component
class TestComponent {
  cardValues: any[];
  buyCards: any[];
  sellCards: any[];

  ngOnChanges(): void {
    if (this.cardValues) {
      this.buyCards = this.cardValues?.filter(
        (card) => card.value.actionToggle === ActionType.Buy
      );
      this.sellCards = this.cardValues?.filter(
        (card) => card.value.actionToggle === ActionType.Sell
      );
    }
  }
}

describe('TestComponent ngOnChanges()', () => {
  let component: TestComponent;

  beforeEach(() => {
    component = new TestComponent();
  });

  it('should correctly filter buy and sell cards', () => {
    component.cardValues = [
      { value: { actionToggle: ActionType.Buy } },
      { value: { actionToggle: ActionType.Sell } },
      { value: { actionToggle: ActionType.Buy } }
    ];

    component.ngOnChanges();

    expect(component.buyCards).toHaveLength(2);
    expect(component.sellCards).toHaveLength(1);
  });

  it('should set both arrays to empty if cardValues is an empty array', () => {
    component.cardValues = [];
    component.ngOnChanges();

    expect(component.buyCards).toEqual([]);
    expect(component.sellCards).toEqual([]);
  });

  it('should not set buyCards or sellCards if cardValues is null', () => {
    component.cardValues = null;
    component.ngOnChanges();

    expect(component.buyCards).toBeUndefined();
    expect(component.sellCards).toBeUndefined();
  });

  it('should ignore unknown actionToggle values', () => {
    component.cardValues = [
      { value: { actionToggle: 'Hold' } },
      { value: { actionToggle: ActionType.Buy } }
    ];
    component.ngOnChanges();

    expect(component.buyCards).toHaveLength(1);
    expect(component.sellCards).toHaveLength(0);
  });
});