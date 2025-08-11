// request-trade.component.spec.ts
import { FormControl, FormGroup, AbstractControl } from '@angular/forms';
import { Subject } from 'rxjs';
import { RequestTradeComponent } from './request-trade.component';

describe('RequestTradeComponent – highlighted search subscriptions', () => {
  let component: RequestTradeComponent;

  const makeGroup = () =>
    new FormGroup({
      search: new FormControl<string | null>(''),
    });

  beforeEach(() => {
    component = new RequestTradeComponent(
      {} as any, // presenter
      {} as any, // modalService
      {} as any, // orderStepPresenter
      {} as any, // analyticsService
      {} as any  // router
    );

    // ensure these exist for the code under test
    (component as any).destroy$ = new Subject<void>();
    (component as any).subscribedControls = new Set<AbstractControl>();

    // avoid unrelated addCard() branch on first line of ngOnChanges
    (component as any).accounts = [];
    (component as any).cards = {} as any;
  });

  // Call the real method; no wrappers
  const runNgOnChanges = () => (component as any).ngOnChanges({} as any);

  it('subscribes to each group.search once and stores the control', () => {
    const g1 = makeGroup();
    const g2 = makeGroup();
    (component as any).cards = { a: g1, b: g2 };

    const spy1 = jest.spyOn(g1.get('search')!.valueChanges, 'subscribe');
    const spy2 = jest.spyOn(g2.get('search')!.valueChanges, 'subscribe');

    runNgOnChanges();

    expect(spy1).toHaveBeenCalledTimes(1);
    expect(spy2).toHaveBeenCalledTimes(1);
    expect((component as any).subscribedControls.has(g1.get('search')!)).toBe(true);
    expect((component as any).subscribedControls.has(g2.get('search')!)).toBe(true);
  });

  it('does not resubscribe when ngOnChanges runs again (Set prevents dupes)', () => {
    const g = makeGroup();
    (component as any).cards = { a: g };

    const subscribeSpy = jest.spyOn(g.get('search')!.valueChanges, 'subscribe');

    runNgOnChanges(); // first pass wires it
    runNgOnChanges(); // second pass should skip (already in Set)

    expect(subscribeSpy).toHaveBeenCalledTimes(1);
  });

  it('calls refreshDuplicateValidation on search value changes', () => {
    const g = makeGroup();
    (component as any).cards = { a: g };

    const refreshSpy = jest.spyOn(component as any, 'refreshDuplicateValidation');

    runNgOnChanges();

    g.get('search')!.setValue('abc');
    g.get('search')!.setValue('def');

    expect(refreshSpy).toHaveBeenCalledTimes(2);
  });

  it('stops reacting after destroy$ emits (takeUntil)', () => {
    const g = makeGroup();
    (component as any).cards = { a: g };

    const refreshSpy = jest.spyOn(component as any, 'refreshDuplicateValidation');

    runNgOnChanges();

    g.get('search')!.setValue('first');
    expect(refreshSpy).toHaveBeenCalledTimes(1);

    (component as any).destroy$.next();
    (component as any).destroy$.complete?.();

    g.get('search')!.setValue('second');
    g.get('search')!.setValue('third');

    expect(refreshSpy).toHaveBeenCalledTimes(1);
  });

  it('ignores groups without a "search" control', () => {
    const noSearch = new FormGroup({ other: new FormControl(1) });
    (component as any).cards = { a: noSearch };

    runNgOnChanges();

    expect((component as any).subscribedControls.size).toBe(0);
  });
});