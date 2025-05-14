import { FormGroup, FormControl, Validators } from '@angular/forms';

// Mock data
const mockAccountPositions = {
  accountPositions: [
    {
      brokerageAccountNumber: '12345678',
      positions: [
        {
          symbol: 'AAPL',
          marketValue: 1000,
          description: 'Apple Inc.',
          type: 'Stock',
          quantity: 10,
          lastPrice: 150,
          holdingPct: 0.25,
        },
      ],
    },
  ],
};

// Stubbed method for getMatchedPosition
const getMatchedPosition = jest.fn((accountPositions, symbol) => {
  return accountPositions.accountPositions[0].positions.find(pos => pos.symbol === symbol);
});

// Custom validator function
const mockValidatorFn = jest.fn((value) => ({ mockError: value }));

// Helper to create the form
const createFormGroup = (actionType = 'Sell', shareToggle = 'Stock', quantity = '') =>
  new FormGroup({
    shareToggle: new FormControl(shareToggle),
    actionToggle: new FormControl(actionType),
    quantity: new FormControl(quantity),
    amount: new FormControl(''),
  });

// Mock sellValidate function using provided code logic
function sellValidate(card, symbol, accountPositions, actionType, unitType, inputField, responseValue, validatorFn) {
  const shareToggle = card.get('shareToggle').value;
  const actionToggle = card.get('actionToggle').value;
  const matchedPosition = getMatchedPosition(accountPositions, symbol);

  if (actionToggle === actionType && shareToggle === unitType) {
    const value = matchedPosition?.[responseValue];
    const field = card.get(inputField);
    field?.setValidators([
      Validators.required,
      value == null
        ? validatorFn(0)
        : validatorFn(responseValue === 'marketValue' ? value * 0.9 : value),
    ]);
  }
}

describe('sellValidate', () => {
  beforeEach(() => {
    mockValidatorFn.mockClear();
  });

  it('should set validators with 90% of marketValue', () => {
    const form = createFormGroup();
    sellValidate(form, 'AAPL', mockAccountPositions, 'Sell', 'Stock', 'amount', 'marketValue', mockValidatorFn);
    
    expect(mockValidatorFn).toHaveBeenCalledWith(900); // 90% of 1000
    expect(form.get('amount').validator).toBeDefined();
  });

  it('should set validators with quantity', () => {
    const form = createFormGroup();
    sellValidate(form, 'AAPL', mockAccountPositions, 'Sell', 'Stock', 'quantity', 'quantity', mockValidatorFn);
    
    expect(mockValidatorFn).toHaveBeenCalledWith(10);
    expect(form.get('quantity').validator).toBeDefined();
  });

  it('should call validatorFn with 0 if no match is found', () => {
    const form = createFormGroup();
    sellValidate(form, 'MSFT', mockAccountPositions, 'Sell', 'Stock', 'quantity', 'quantity', mockValidatorFn);

    expect(mockValidatorFn).toHaveBeenCalledWith(0);
  });

  it('should not set validators if action or unitType mismatch', () => {
    const form = createFormGroup('Buy', 'Bond');
    sellValidate(form, 'AAPL', mockAccountPositions, 'Sell', 'Stock', 'quantity', 'quantity', mockValidatorFn);

    expect(mockValidatorFn).not.toHaveBeenCalled();
  });
});