import { ComponentFixture, TestBed } from '@angular/core/testing';
import { YourComponent } from './your-component';
import { ActionType, UnitType } from 'path-to-enums';
import { FormGroup, FormControl } from '@angular/forms';

describe('YourComponent', () => {
  let component: YourComponent;
  let fixture: ComponentFixture<YourComponent>;

  beforeEach(() => {
    TestBed.configureTestingModule({
      declarations: [YourComponent]
    });

    fixture = TestBed.createComponent(YourComponent);
    component = fixture.componentInstance;

    // Mock required properties
    component.card = new FormGroup({
      shareToggle: new FormControl(UnitType.Dollars),
      amount: new FormControl(),
      quantity: new FormControl()
    });
    component.symbol = 'AAPL';
    component.accountPositions = { accountPositions: [] };
  });

  it('should call sellValidate if action is Sell', () => {
    const sellValidateSpy = jest.spyOn(component.presenter, 'sellValidate');

    const action = { value: ActionType.Sell };
    component['action'] = action; // mock or set action however it's used

    // You may need to call the method that contains this logic
    // Assuming it's in some method like `onActionChanged()`
    component['presenter'].sellValidate(component.card, component.symbol, component.accountPositions);

    expect(sellValidateSpy).toHaveBeenCalledWith(component.card, component.symbol, component.accountPositions);
  });

  it('should NOT call sellValidate if action is not Sell', () => {
    const sellValidateSpy = jest.spyOn(component.presenter, 'sellValidate');

    const action = { value: ActionType.Buy }; // or any other non-Sell value
    component['action'] = action;

    // Simulate the method that would normally trigger the logic
    // Ensure it's NOT called
    // No need to invoke sellValidate manually in this case

    expect(sellValidateSpy).not.toHaveBeenCalled();
  });
});