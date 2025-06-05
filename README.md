import { ComponentFixture, TestBed } from '@angular/core/testing';
import { TradeOrderSuccessComponent } from './trade-order-success.component';
import { FormGroup, FormControl, ReactiveFormsModule } from '@angular/forms';
import { ActionType } from 'src/app/shared/enums'; // Adjust path to your project
import { OrderStepsPresenter } from 'src/app/presenters/order-steps.presenter';

describe('TradeOrderSuccessComponent', () => {
  let component: TradeOrderSuccessComponent;
  let fixture: ComponentFixture<TradeOrderSuccessComponent>;
  let mockPresenter: any;

  beforeEach(async () => {
    mockPresenter = {
      getCardInfoByAction: jest.fn()
    };

    await TestBed.configureTestingModule({
      declarations: [TradeOrderSuccessComponent],
      imports: [ReactiveFormsModule],
      providers: [
        { provide: OrderStepsPresenter, useValue: mockPresenter }
      ]
    }).compileComponents();

    fixture = TestBed.createComponent(TradeOrderSuccessComponent);
    component = fixture.componentInstance;
  });

  it('should process cardValues correctly on ngOnInit', () => {
    // Arrange
    const buyCard = new FormGroup({
      actionToggle: new FormControl(ActionType.Buy)
    });

    const sellCard = new FormGroup({
      actionToggle: new FormControl(ActionType.Sell)
    });

    // Add 'value' wrapper to simulate form structure
    component.cardValues = [
      { value: buyCard.value } as any,
      { value: sellCard.value } as any
    ];

    // Act
    component.ngOnInit();

    // Assert
    expect(component.buyCards.length).toBe(1);
    expect(component.sellCards.length).toBe(1);
    expect(component.buyCards[0].value.actionToggle).toBe(ActionType.Buy);
    expect(mockPresenter.getCardInfoByAction).toHaveBeenCalledWith(component.cardValues);
  });
});