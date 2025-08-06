import { FormGroup, FormControl } from '@angular/forms';
import { MyComponent } from './my-component';  // Adjust import accordingly

describe('MyComponent', () => {
  let component: MyComponent;

  beforeEach(() => {
    component = new MyComponent();

    // Mock cards with FormGroups and search FormControls
    component.cards = {
      card1: new FormGroup({
        search: new FormControl('value1')
      }),
      card2: new FormGroup({
        search: new FormControl('value2')
      }),
      card3: new FormGroup({
        search: new FormControl('value1')  // duplicate value to simulate validation error
      })
    };
  });

  it('should call updateValueAndValidity for controls with duplicate errors', () => {
    // Set duplicate errors manually to simulate validation
    component.cards.card1.get('search')?.setErrors({ duplicate: true });
    component.cards.card2.get('search')?.setErrors(null);  // no error
    component.cards.card3.get('search')?.setErrors({ duplicate: true });

    const updateSpy1 = jest.spyOn(component.cards.card1.get('search')!, 'updateValueAndValidity');
    const updateSpy2 = jest.spyOn(component.cards.card2.get('search')!, 'updateValueAndValidity');
    const updateSpy3 = jest.spyOn(component.cards.card3.get('search')!, 'updateValueAndValidity');

    component.refreshDuplicateValidation();

    expect(updateSpy1).toHaveBeenCalledWith({ onlySelf: true, emitEvent: false });
    expect(updateSpy2).not.toHaveBeenCalled();  // No duplicate error, shouldn't be called
    expect(updateSpy3).toHaveBeenCalledWith({ onlySelf: true, emitEvent: false });
  });

  it('should not call updateValueAndValidity if no controls have duplicate errors', () => {
    component.cards.card1.get('search')?.setErrors(null);
    component.cards.card2.get('search')?.setErrors(null);
    component.cards.card3.get('search')?.setErrors(null);

    const updateSpy1 = jest.spyOn(component.cards.card1.get('search')!, 'updateValueAndValidity');
    const updateSpy2 = jest.spyOn(component.cards.card2.get('search')!, 'updateValueAndValidity');
    const updateSpy3 = jest.spyOn(component.cards.card3.get('search')!, 'updateValueAndValidity');

    component.refreshDuplicateValidation();

    expect(updateSpy1).not.toHaveBeenCalled();
    expect(updateSpy2).not.toHaveBeenCalled();
    expect(updateSpy3).not.toHaveBeenCalled();
  });

  it('should safely handle if search control is missing in a group', () => {
    // Remove search control from one card to simulate missing control
    component.cards.card2.removeControl('search');

    const updateSpy1 = jest.spyOn(component.cards.card1.get('search')!, 'updateValueAndValidity');
    const updateSpy3 = jest.spyOn(component.cards.card3.get('search')!, 'updateValueAndValidity');

    component.cards.card1.get('search')?.setErrors({ duplicate: true });
    component.cards.card3.get('search')?.setErrors({ duplicate: true });

    component.refreshDuplicateValidation();

    expect(updateSpy1).toHaveBeenCalledWith({ onlySelf: true, emitEvent: false });
    expect(updateSpy3).toHaveBeenCalledWith({ onlySelf: true, emitEvent: false });
  });
});