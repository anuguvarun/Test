import { FormControl, FormGroup, FormArray, ValidatorFn, AbstractControl } from '@angular/forms';
import { duplicateSearchValidator } from './your-validator-file'; // Replace with your actual file path

describe('duplicateSearchValidator', () => {
  const buildCard = (search: string, tradeType: string): FormGroup =>
    new FormGroup({
      search: new FormControl(search),
      tradeType: new FormControl(tradeType),
    });

  const getCardsFactory = (cards: FormGroup[]) => () => cards;

  const setup = (cards: FormGroup[], indexToTest: number, newSearch: string, newTradeType: string) => {
    const formArray = new FormArray(cards);
    const group = formArray.at(indexToTest) as FormGroup;

    group.get('search')?.setValue(newSearch);
    group.get('tradeType')?.setValue(newTradeType);

    const validator = duplicateSearchValidator(getCardsFactory(cards));
    return validator(group.get('search')!);
  };

  it('should return null if cards list is empty', () => {
    const validator = duplicateSearchValidator(() => []);
    const dummyControl = new FormControl('test');
    expect(validator(dummyControl)).toBeNull();
  });

  it('should return null if control.parent is not in cards', () => {
    const cards = [buildCard('apple', 'buy')];
    const validator = duplicateSearchValidator(getCardsFactory(cards));

    const orphanGroup = new FormGroup({
      search: new FormControl('apple'),
    });
    const control = orphanGroup.get('search')!;

    expect(validator(control)).toBeNull();
  });

  it('should return null if currentSearch is empty', () => {
    const cards = [buildCard('', 'buy')];
    const result = setup(cards, 0, '', 'buy');
    expect(result).toBeNull();
  });

  it('should return null if currentTradeType is empty', () => {
    const cards = [buildCard('apple', '')];
    const result = setup(cards, 0, 'apple', '');
    expect(result).toBeNull();
  });

  it('should return null if no duplicates exist', () => {
    const cards = [
      buildCard('apple', 'buy'),
      buildCard('banana', 'sell'),
    ];
    const result = setup(cards, 0, 'apple', 'buy');
    expect(result).toBeNull();
  });

  it('should return null for the first instance of a duplicate', () => {
    const cards = [
      buildCard('apple', 'buy'),
      buildCard('apple', 'buy'),
    ];
    const result = setup(cards, 0, 'apple', 'buy');
    expect(result).toBeNull();
  });

  it('should return validation error for duplicate entry that is not the first', () => {
    const cards = [
      buildCard('apple', 'buy'),
      buildCard('apple', 'buy'),
    ];
    const result = setup(cards, 1, 'apple', 'buy');
    expect(result).toEqual({ duplicate: true });
  });

  it('should trim and lowercase the search value when checking for duplicates', () => {
    const cards = [
      buildCard('  Apple  ', 'buy'),
      buildCard('apple', 'buy'),
    ];
    const result = setup(cards, 1, 'apple', 'buy');
    expect(result).toEqual({ duplicate: true });
  });

  it('should treat tradeType as case-sensitive', () => {
    const cards = [
      buildCard('apple', 'BUY'),
      buildCard('apple', 'buy'),
    ];
    const result = setup(cards, 1, 'apple', 'buy');
    expect(result).toBeNull(); // only if tradeType is case-sensitive
  });

  it('should handle different tradeType values with same search as unique', () => {
    const cards = [
      buildCard('apple', 'buy'),
      buildCard('apple', 'sell'),
    ];
    const result = setup(cards, 1, 'apple', 'sell');
    expect(result).toBeNull();
  });
});