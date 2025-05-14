import { FormGroup } from '@angular/forms';
import { YourComponentOrService } from './your-file';
import { AccountPositionsResponseInfo } from './your-types';
import { ActionType, UnitType } from './your-enums'; // Adjust paths accordingly
import { Validators } from '@angular/forms';

describe('YourComponentOrService', () => {
  let instance: YourComponentOrService;

  beforeEach(() => {
    instance = new YourComponentOrService();
    jest.spyOn(instance, 'sellValidate'); // spy on sellValidate
  });

  it('should call sellValidate with correct arguments', () => {
    const mockCard = {} as FormGroup;
    const mockSymbol = 'AAPL';
    const mockAccountPositions = {} as AccountPositionsResponseInfo;

    instance.sellShareValidate(mockCard, mockSymbol, mockAccountPositions);

    expect(instance.sellValidate).toHaveBeenCalledWith(
      mockCard,
      mockSymbol,
      mockAccountPositions,
      ActionType.Sell,
      UnitType.Shares,
      'quantity',
      'quantity',
      Validators.max
    );
  });
});