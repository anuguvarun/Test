import { FormControl, FormGroup } from '@angular/forms';
import { duplicateSearchValidator } from './your-validator-file'; // Update with actual file path

describe('duplicateSearchValidator', () => {
  let mockGetCards: jest.Mock;

  beforeEach(() => {
    mockGetCards = jest.fn();
  });

  function createForm(search: string | null, tradeType: string | null): FormGroup {
    return new FormGroup({
      search: new FormControl(search),
      tradeType: new FormControl(tradeType),
    });
  }

  it('should return null if cards are empty', () => {
    mockGetCards.mockReturnValue([]);
    const control = new FormControl('test');
    const validator = duplicateSearchValidator(mockGetCards);
    expect(validator(control)).toBeNull();
  });

  it('should return null if group is not found in cards', () => {
    const group = createForm('test', 'typeA');
    mockGetCards.mockReturnValue([]);
    const control = new FormControl('test');
    control.setParent(group);
    const validator = duplicateSearchValidator(mockGetCards);
    expect(validator(control)).toBeNull();
  });

  it('should return null if currentSearch or currentTradeType is missing', () => {
    const group = createForm(null, null);
    mockGetCards.mockReturnValue([group]);
    const control = group.get('search')!;
    const validator = duplicateSearchValidator(mockGetCards);
    expect(validator(control)).toBeNull();
  });

  it('should return null if no duplicates are found', () => {
    const group1 = createForm('test1', 'typeA');
    const group2 = createForm('test2', 'typeB');
    mockGetCards.mockReturnValue([group1, group2]);

    const control = group1.get('search')!;
    const validator = duplicateSearchValidator(mockGetCards);
    expect(validator(control)).toBeNull();
  });

  it('should return { duplicate: true } if duplicate found', () => {
    const group1 = createForm('duplicate', 'typeA');
    const group2 = createForm('duplicate', 'typeA');

    mockGetCards.mockReturnValue([group1, group2]);

    const control = group1.get('search')!;
    const validator = duplicateSearchValidator(mockGetCards);
    expect(validator(control)).toEqual({ duplicate: true });
  });
});