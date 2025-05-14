import { Validators, FormGroup } from '@angular/forms';
import { YourPresenter } from './your-presenter-file';

describe('YourPresenter', () => {
  let presenter: YourPresenter;

  beforeEach(() => {
    presenter = new YourPresenter();
  });

  it('should apply validators when action and unit types match', () => {
    const mockCard = {
      get: jest.fn((field: string) => ({
        value: field === 'shareToggle' ? 'unitType1' : 'actionType1',
        setValidators: jest.fn()
      }))
    } as unknown as FormGroup;

    const mockAccountPositions = { /* mock data */ };
    const matchedPosition = {
      quantity: 100,
      marketValue: 200
    };

    presenter.getMatchedPosition = jest.fn().mockReturnValue(matchedPosition);

    const validatorFn = jest.fn().mockImplementation((val: number) => `validated-${val}`);

    presenter.sellValidate(
      mockCard,
      'AAPL',
      mockAccountPositions,
      'actionType1',
      'unitType1',
      'quantity',
      'quantity',
      validatorFn
    );

    expect(validatorFn).toHaveBeenCalledWith(100);
    expect(mockCard.get('quantity')?.setValidators).toHaveBeenCalledWith([
      Validators.required,
      'validated-100'
    ]);
  });

  it('should apply validatorFn with 0 if value is null', () => {
    const mockCard = {
      get: jest.fn((field: string) => ({
        value: field === 'shareToggle' ? 'unitType1' : 'actionType1',
        setValidators: jest.fn()
      }))
    } as unknown as FormGroup;

    const matchedPosition = {
      marketValue: null
    };

    presenter.getMatchedPosition = jest.fn().mockReturnValue(matchedPosition);

    const validatorFn = jest.fn().mockImplementation((val: number) => `validated-${val}`);

    presenter.sellValidate(
      mockCard,
      'AAPL',
      {},
      'actionType1',
      'unitType1',
      'amount',
      'marketValue',
      validatorFn
    );

    expect(validatorFn).toHaveBeenCalledWith(0);
    expect(mockCard.get('amount')?.setValidators).toHaveBeenCalledWith([
      Validators.required,
      'validated-0'
    ]);
  });

  it('should use 90% of marketValue if responseValue is marketValue and not null', () => {
    const mockCard = {
      get: jest.fn((field: string) => ({
        value: field === 'shareToggle' ? 'unitType1' : 'actionType1',
        setValidators: jest.fn()
      }))
    } as unknown as FormGroup;

    const matchedPosition = {
      marketValue: 500
    };

    presenter.getMatchedPosition = jest.fn().mockReturnValue(matchedPosition);

    const validatorFn = jest.fn().mockImplementation((val: number) => `validated-${val}`);

    presenter.sellValidate(
      mockCard,
      'GOOG',
      {},
      'actionType1',
      'unitType1',
      'quantity',
      'marketValue',
      validatorFn
    );

    expect(validatorFn).toHaveBeenCalledWith(500 * 0.9);
    expect(mockCard.get('quantity')?.setValidators).toHaveBeenCalledWith([
      Validators.required,
      'validated-450'
    ]);
  });
});