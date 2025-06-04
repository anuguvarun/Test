import { YourComponent } from './your.component';
import { OrderStepsPresenter } from './order-steps.presenter';
import { ActionType } from './action-type.enum';

describe('YourComponent', () => {
  let component: YourComponent;
  let mockOrderStepPresenter: jest.Mocked<OrderStepsPresenter>;

  beforeEach(() => {
    mockOrderStepPresenter = {
      getCardInfoByAction: jest.fn()
    } as any;

    component = new YourComponent(mockOrderStepPresenter);

    component.cardValues = [
      { value: { actionToggle: ActionType.Buy } },
      { value: { actionToggle: ActionType.Sell } },
      { value: { actionToggle: ActionType.Buy } }
    ];
  });

  it('should separate buy and sell cards on ngOnInit', () => {
    component.ngOnInit();

    expect(component.buyCards.length).toBe(2);
    expect(component.sellCards.length).toBe(1);

    expect(component.buyCards.every(card => card.value.actionToggle === ActionType.Buy)).toBe(true);
    expect(component.sellCards.every(card => card.value.actionToggle === ActionType.Sell)).toBe(true);
  });

  it('should call getCardInfoByAction with cardValues', () => {
    component.ngOnInit();
    expect(mockOrderStepPresenter.getCardInfoByAction).toHaveBeenCalledWith(component.cardValues);
  });
});