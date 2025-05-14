const mockGetMatchedPosition = jest.fn();

const fb = new FormBuilder();

const mockCard = fb.group({
  shareToggle: ['shares'],
  actionToggle: ['sell'],
  quantity: new FormControl(),
  amount: new FormControl(),
});

const accountPositions = {
  AAPL: {
    quantity: 100,
    marketValue: 10000,
  }
};

const mockValidatorFn = jest.fn((val) => Validators.min(val));

describe('sellValidate', () => {
  beforeEach(() => {
    jest.clearAllMocks();
  });

  it('should set validators with 90% market value if responseValue is marketValue', () => {
    const symbol = 'AAPL';
    const actionType = 'sell';
    const unitType = 'shares';
    const inputField = 'quantity';
    const responseValue = 'marketValue';

    const matchedPosition = {
      marketValue: 1000
    };

    const spy = jest.spyOn<any, any>(sellValidate.prototype || {}, 'getMatchedPosition').mockReturnValue(matchedPosition);

    sellValidate(
      mockCard,
      symbol,
      accountPositions,
      actionType,
      unitType,
      inputField,
      responseValue,
      mockValidatorFn
    );

    const control = mockCard.get(inputField);
    expect(control?.validator).toBeDefined();
    const validator = control?.validator!;
    const result = validator({ value: null });
    expect(mockValidatorFn).toHaveBeenCalledWith(900); // 90% of 1000
  });

  it('should set validators with original value if responseValue is quantity', () => {
    const symbol = 'AAPL';
    const actionType = 'sell';
    const unitType = 'shares';
    const inputField = 'amount';
    const responseValue = 'quantity';

    const matchedPosition = {
      quantity: 200
    };

    jest.spyOn<any, any>(sellValidate.prototype || {}, 'getMatchedPosition').mockReturnValue(matchedPosition);

    sellValidate(
      mockCard,
      symbol,
      accountPositions,
      actionType,
      unitType,
      inputField,
      responseValue,
      mockValidatorFn
    );

    expect(mockValidatorFn).toHaveBeenCalledWith(200);
  });

  it('should set validatorFn with 0 if value is null', () => {
    const symbol = 'AAPL';
    const actionType = 'sell';
    const unitType = 'shares';
    const inputField = 'quantity';
    const responseValue = 'marketValue';

    const matchedPosition = {
      marketValue: null
    };

    jest.spyOn<any, any>(sellValidate.prototype || {}, 'getMatchedPosition').mockReturnValue(matchedPosition);

    sellValidate(
      mockCard,
      symbol,
      accountPositions,
      actionType,
      unitType,
      inputField,
      responseValue,
      mockValidatorFn
    );

    expect(mockValidatorFn).toHaveBeenCalledWith(0);
  });
});
