import { FormControl, FormGroup } from '@angular/forms';
import { duplicateSearchValidator } from './duplicateSearchValidator';

describe('duplicateSearchValidator (ticker symbols)', () => {
  const createGroup = (ticker: string): FormGroup =>
    new FormGroup({
      search: new FormControl(ticker)
    });

  it('should return null if the ticker is empty', () => {
    const control = new FormControl('');
    const result = duplicateSearchValidator(0, [createGroup('AAPL')])(control);
    expect(result).toBeNull();
  });

  it('should return null if no duplicate ticker is found', () => {
    const cards = [createGroup('AAPL'), createGroup('GOOGL'), createGroup('MSFT')];
    const control = new FormControl('TSLA');
    const result = duplicateSearchValidator(3, cards)(control);
    expect(result).toBeNull();
  });

  it('should return { duplicate: true } if ticker is a duplicate', () => {
    const cards = [createGroup('AAPL'), createGroup('GOOGL'), createGroup('MSFT')];
    const control = new FormControl('GOOGL');
    const result = duplicateSearchValidator(0, cards)(control);
    expect(result).toEqual({ duplicate: true });
  });

  it('should ignore the current index when checking for duplicates', () => {
    const cards = [createGroup('AAPL'), createGroup('GOOGL'), createGroup('MSFT')];
    const control = new FormControl('GOOGL');
    const result = duplicateSearchValidator(1, cards)(control); // same index
    expect(result).toBeNull();
  });

  it('should detect duplicates regardless of case or whitespace', () => {
    const cards = [createGroup('  msft ')];
    const control = new FormControl('MSFT');
    const result = duplicateSearchValidator(1, cards)(control);
    expect(result).toEqual({ duplicate: true });
  });
});