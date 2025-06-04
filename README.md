import { ComponentFixture, TestBed } from '@angular/core/testing';
import { FormGroup, FormControl } from '@angular/forms';
import { ActionType } from './action-type.enum'; // adjust path as needed
import { TradeOrderSuccessComponent } from './trade-order-success.component'; // adjust component name & path
import { OrderStepsPresenter } from './order-steps-presenter'; // adjust path as needed

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

    // Initialize mock cardValues as FormGroups before detectChanges
    component['cardValues'] = [
      new FormGroup({
        actionToggle: new FormControl(ActionType.Buy)
      }),
      new FormGroup({
        actionToggle: new FormControl(ActionType.Sell)
      }),
      new FormGroup({
        actionToggle: new FormControl(ActionType.Buy)
      })
    ];
  });

  it('should create', () => {
    fixture.detectChanges();
    expect(component).toBeTruthy();
  });

  it('should filter buy and sell cards correctly on ngOnInit', () => {
    component.ngOnInit();

    expect(component.buyCards.length).toBe(2);
    expect(component.sellCards.length).toBe(1);

    expect(component.buyCards.map(card => card.value)).toEqual([
      { actionToggle: ActionType.Buy },
      { actionToggle: ActionType.Buy }
    ]);

    expect(component.sellCards.map(card => card.value)).toEqual([
      { actionToggle: ActionType.Sell }
    ]);
  });

  it('should call getCardInfoByAction with cardValues', () => {
    component.ngOnInit();

    expect(mockOrderStepPresenter.getCardInfoByAction).toHaveBeenCalledWith(component['cardValues']);
  });
});