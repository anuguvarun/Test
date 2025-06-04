import { ComponentFixture, TestBed } from '@angular/core/testing';
import { YourComponent } from './your.component';
import { OrderStepsPresenter } from './order-steps-presenter';
import { ActionType } from './action-type.enum'; // Adjust path accordingly
import { of } from 'rxjs';

describe('YourComponent', () => {
  let component: YourComponent;
  let fixture: ComponentFixture<YourComponent>;
  let mockOrderStepsPresenter: any;

  beforeEach(() => {
    mockOrderStepsPresenter = {
      getCardInfoByAction: jest.fn()
    };

    TestBed.configureTestingModule({
      declarations: [YourComponent],
      providers: [
        { provide: OrderStepsPresenter, useValue: mockOrderStepsPresenter }
      ]
    });

    fixture = TestBed.createComponent(YourComponent);
    component = fixture.componentInstance;
  });

  it('should filter buy and sell cards correctly on ngOnInit', () => {
    component['cardValues'] = [
      { value: { actionToggle: ActionType.Buy } },
      { value: { actionToggle: ActionType.Sell } },
      { value: { actionToggle: ActionType.Buy } }
    ];

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
    component['cardValues'] = [];

    component.ngOnInit();

    expect(mockOrderStepsPresenter.getCardInfoByAction).toHaveBeenCalledWith([]);
  });
});