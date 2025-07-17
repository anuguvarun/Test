import { FormGroup, FormControl, Validators, FormArray } from '@angular/forms';
import { duplicateSearchValidator } from './your-validator-file'; // adjust import as needed
import { YourComponent } from './your-component-file';

jest.mock('./your-validator-file', () => ({
  duplicateSearchValidator: jest.fn(),
}));

describe('YourComponent', () => {
  let component: YourComponent;

  beforeEach(() => {
    component = new YourComponent();

    // Set up mock cards FormArray with FormGroups
    component.cards = new FormArray([
      new FormGroup({ search: new FormControl('') }),
      new FormGroup({ search: new FormControl('') }),
    ]);
  });

  it('should set validators, update value and mark control as touched', () => {
    const index = 0;
    const searchControl = component.cards.at(index).get('search') as FormControl;

    // Spies
    const setValidatorsSpy = jest.spyOn(searchControl, 'setValidators');
    const updateValueAndValiditySpy = jest.spyOn(searchControl, 'updateValueAndValidity');
    const markAsTouchedSpy = jest.spyOn(searchControl, 'markAsTouched');

    // Mock return of validator
    (duplicateSearchValidator as jest.Mock).mockReturnValue(() => null);

    // Call function
    component.duplicateSearch(index);

    // Assertions
    expect(setValidatorsSpy).toHaveBeenCalledWith([
      Validators.required,
      expect.any(Function),
    ]);
    expect(duplicateSearchValidator).toHaveBeenCalledWith(index, component.cards);
    expect(updateValueAndValiditySpy).toHaveBeenCalled();
    expect(markAsTouchedSpy).toHaveBeenCalled();
  });
});