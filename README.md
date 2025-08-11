// refresh-duplicate-validation.spec.ts
import { FormControl, FormGroup } from '@angular/forms';
// ⬇️ change this to your class/component
import { YourClass } from './your-class';

describe('refreshDuplicateValidation', () => {
  it('updates only controls that have a duplicate error', () => {
    const sut = new YourClass();

    const g1 = new FormGroup({ search: new FormControl('a') });
    const g2 = new FormGroup({ search: new FormControl('b') });
    const g3 = new FormGroup({ search: new FormControl('c') });

    g1.get('search')!.setErrors({ duplicate: true });
    g2.get('search')!.setErrors({ duplicate: true, required: true });
    g3.get('search')!.setErrors({ required: true });

    const s1 = jest.spyOn(g1.get('search')!, 'updateValueAndValidity');
    const s2 = jest.spyOn(g2.get('search')!, 'updateValueAndValidity');
    const s3 = jest.spyOn(g3.get('search')!, 'updateValueAndValidity');

    sut.cards = { g1, g2, g3 } as any;

    sut.refreshDuplicateValidation();

    expect(s1).toHaveBeenCalledWith({ onlySelf: true, emitEvent: false });
    expect(s2).toHaveBeenCalledWith({ onlySelf: true, emitEvent: false });
    expect(s3).not.toHaveBeenCalled();
  });

  it('does nothing when there are no duplicate errors', () => {
    const sut = new YourClass();

    const g1 = new FormGroup({ search: new FormControl('a') });
    const g2 = new FormGroup({ search: new FormControl('b') });

    g1.get('search')!.setErrors(null);
    g2.get('search')!.setErrors({ required: true });

    const s1 = jest.spyOn(g1.get('search')!, 'updateValueAndValidity');
    const s2 = jest.spyOn(g2.get('search')!, 'updateValueAndValidity');

    sut.cards = { g1, g2 } as any;

    sut.refreshDuplicateValidation();

    expect(s1).not.toHaveBeenCalled();
    expect(s2).not.toHaveBeenCalled();
  });

  it('skips groups without a "search" control', () => {
    const sut = new YourClass();

    const withSearch = new FormGroup({ search: new FormControl('dup') });
    withSearch.get('search')!.setErrors({ duplicate: true });

    const withoutSearch = new FormGroup({ other: new FormControl('x') });

    const spy = jest.spyOn(withSearch.get('search')!, 'updateValueAndValidity');

    sut.cards = { withSearch, withoutSearch } as any;

    expect(() => sut.refreshDuplicateValidation()).not.toThrow();
    expect(spy).toHaveBeenCalledWith({ onlySelf: true, emitEvent: false });
  });

  it('handles null/undefined errors safely', () => {
    const sut = new YourClass();

    const g = new FormGroup({ search: new FormControl('ok') });
    g.get('search')!.setErrors(null);

    const spy = jest.spyOn(g.get('search')!, 'updateValueAndValidity');

    sut.cards = { g } as any;

    sut.refreshDuplicateValidation();

    expect(spy).not.toHaveBeenCalled();
  });
});