import { FormControl, FormGroup, FormArray, Validators, AbstractControl, ValidationErrors } from '@angular/forms';
import { duplicateSearchValidator } from './your-validator-file'; // replace with actual path

describe('duplicateSearchValidator', () => {
  const buildCard = (search: string, tradeType: string): FormGroup =>
    new FormGroup({
      search: new FormControl(search),
      tradeType: new FormControl(tradeType),
    });

  const getCardsFactory = (cards: FormGroup[]) => () => cards;

  const setup = (cards: FormGroup[], indexToTest: number, search: string, tradeType: string) => {
    const targetCard = cards[indexToTest];
    targetCard.get('search')?.setValue(search);
    targetCard.get('tradeType')?.setValue(tradeType);
    const validator = duplicateSearchValidator(getCardsFactory(cards));
    return validator(targetCard.get('search')!);
  };

  it('should return null if cards are empty', () => {
    const validator = duplicateSearchValidator(() => []);
    const control = new FormControl('test');
    expect(validator(control)).toBeNull();
  });

  it('should return null if index not found in cards', () => {
    const cards = [buildCard('a', 'type1')];
    const validator = duplicateSearchValidator(getCardsFactory(cards));
    const control = new FormControl('test');
    const group = new FormGroup({ search: control });
    control.parent = group; // parent is different from any in `cards`
    expect(validator(control)).toBeNull();
  });

  it('should return null if currentSearch is falsy', () => {
    const cards = [buildCard('', 'type1')];
    const result = setup(cards, 0, '', 'type1');
    expect(result).toBeNull();
  });

  it('should return null if currentTradeType is falsy', () => {
    const cards = [buildCard('search', '')];
    const result = setup(cards, 0, 'search', '');
    expect(result).toBeNull();
  });

  it('should return null if no duplicates are found', () => {
    const cards = [
      buildCard('apple', 'buy'),
      buildCard('banana', 'sell'),
    ];
    const result = setup(cards, 0, 'apple', 'buy');
    expect(result).toBeNull();
  });

  it('should return error if duplicate is found (not first occurrence)', () => {
    const cards = [
      buildCard('apple', 'buy'),
      buildCard('apple', 'buy'),
    ];
    const result = setup(cards, 1, 'apple', 'buy');
    expect(result).toEqual({ duplicate: true });
  });

  it('should return null for the first occurrence of a duplicate', () => {
    const cards = [
      buildCard('apple', 'buy'),
      buildCard('apple', 'buy'),
    ];
    const result = setup(cards, 0, 'apple', 'buy');
    expect(result).toBeNull();
  });

  it('should treat search case-insensitively and trim whitespace', () => {
    const cards = [
      buildCard(' Apple ', 'buy'),
      buildCard('apple', 'buy'),
    ];
    const result = setup(cards, 1, 'apple', 'buy');
    expect(result).toEqual({ duplicate: true });
  });

  it('should treat tradeType as case-sensitive (if expected)', () => {
    const cards = [
      buildCard('apple', 'BUY'),
      buildCard('apple', 'buy'),
    ];
    const result = setup(cards, 1, 'apple', 'buy');
    expect(result).toBeNull(); // Only if tradeType is case-sensitive
  });
});