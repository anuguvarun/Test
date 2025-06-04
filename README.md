import { MyComponent } from './my.component'; // Replace with actual component name
import { ActionType } from './action-type.enum'; // Replace with actual enum import

describe('MyComponent', () => {
  let component: MyComponent;

  beforeEach(() => {
    component = new MyComponent({ getCardInfoByAction: jest.fn() } as any); // Mock the presenter
    component.cardValues = [
      { value: { actionToggle: ActionType.Buy } },
      { value: { actionToggle: ActionType.Sell } },
      { value: { actionToggle: ActionType.Buy } },
    ];
  });

  it('should populate buyCards with cards that have actionToggle = Buy', () => {
    component.ngOnInit();
    expect(component.buyCards.length).toBe(2);
    expect(component.buyCards.every(c => c.value.actionToggle === ActionType.Buy)).toBe(true);
  });

  it('should populate sellCards with cards that have actionToggle = Sell', () => {
    component.ngOnInit();
    expect(component.sellCards.length).toBe(1);
    expect(component.sellCards[0].value.actionToggle).toBe(ActionType.Sell);
  });

  it('should call getCardInfoByAction with the full cardValues array', () => {
    component.ngOnInit();
    expect(component.orderStepPresenter.getCardInfoByAction).toHaveBeenCalledWith(component.cardValues);
  });
});