import { FormGroup } from '@angular/forms';
import { sellValidate } from './your-file'; // adjust path as needed

describe('sellValidate', () => {
  let mockForm: any;
  let mockValidatorFn: jest.Mock;
  let mockGetMatchedPosition: jest.SpyInstance;

  beforeEach(() => {
    mockValidatorFn = jest.fn();

    mockForm = {
      get: jest.fn((key: string) => ({
        value: {
          shareToggle: 'quantity',
          actionToggle: 'SELL',
        }[key],
        setValidators: jest.fn(),
      })),
    };

    mockGetMatchedPosition = jest.spyOn<any, any>(YourClass.prototype, 'getMatchedPosition');
  });

  afterEach(() => {
    jest.clearAllMocks();
  });

  it('should set validators when actionType and unitType match toggles', () => {
    const mockPosition = {
      quantity: 100,
      marketValue: 2000,
    };

    mockForm.get = jest.fn((key: string) => {
      if (key === 'shareToggle') return { value: 'quantity' };
      if (key === 'actionToggle') return { value: 'SELL' };
      if (key === 'quantity') return { setValidators: jest.fn() };
      return null;
    });

    mockGetMatchedPosition.mockReturnValue(mockPosition);

    sellValidate(
      mockForm,
      'AAPL',
      {} as any,
      'SELL',
      'quantity',
      'quantity',
      mockValidatorFn
    );

    const field = mockForm.get('quantity');
    expect(field.setValidators).toHaveBeenCalledWith([
      expect.any(Function), // Validators.required
      mockValidatorFn(100), // responseValue is 'quantity'
    ]);
  });

  it('should use default value when matched position is null', () => {
    mockGetMatchedPosition.mockReturnValue(null);

    mockForm.get = jest.fn((key: string) => {
      if (key === 'shareToggle') return { value: 'quantity' };
      if (key === 'actionToggle') return { value: 'SELL' };
      if (key === 'quantity') return { setValidators: jest.fn() };
      return null;
    });

    sellValidate(
      mockForm,
      'AAPL',
      {} as any,
      'SELL',
      'quantity',
      'quantity',
      mockValidatorFn
    );

    const field = mockForm.get('quantity');
    expect(field.setValidators).toHaveBeenCalledWith([
      expect.any(Function),
      mockValidatorFn(0),
    ]);
  });

  it('should adjust value for marketValue', () => {
    const mockPosition = {
      marketValue: 1000,
    };

    mockGetMatchedPosition.mockReturnValue(mockPosition);

    mockForm.get = jest.fn((key: string) => {
      if (key === 'shareToggle') return { value: 'amount' };
      if (key === 'actionToggle') return { value: 'SELL' };
      if (key === 'amount') return { setValidators: jest.fn() };
      return null;
    });

    sellValidate(
      mockForm,
      'AAPL',
      {} as any,
      'SELL',
      'amount',
      'marketValue',
      mockValidatorFn
    );

    const field = mockForm.get('amount');
    expect(field.setValidators).toHaveBeenCalledWith([
      expect.any(Function),
      mockValidatorFn(900), // 0.9 * 1000
    ]);
  });
});