import { FormGroup, FormControl } from '@angular/forms';

const mockForm = new FormGroup({
  actionToggle: new FormControl(),
  shareToggle: new FormControl(),
  amount: new FormControl(),
  quantity: new FormControl()
});

component.presenter.updateFormFieldStatus(
  mockForm,
  ['actionToggle', 'shareToggle', 'amount', 'quantity'],
  false
);