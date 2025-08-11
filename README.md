import { Subject } from 'rxjs';
import { RequestTradeComponent } from './request-trade.component';
import { stubCard } from './path-to-your-stub-card-file'; // adjust import

describe('RequestTradeComponent – highlighted search subscriptions', () => {
  let component: RequestTradeComponent;

  beforeEach(() => {
    component = new RequestTradeComponent(
      {} as any, // presenter
      {} as any, // modalService
      {} as any, // orderStepPresenter
      {} as any, // analyticsService
      {} as any  // router
    );
    (component as any).destroy$ = new Subject<void>();
    (component as any).subscribedControls = new Set<any>();
    (component as any).accounts = [];
    (component as any).cards = {} as any;
  });

  const runNgOnChanges = () => (component as any).ngOnChanges({} as any);

  it('subscribes to each stubCard search control', () => {
    const card1 = stubCard('Buy', 'Action1', 'Share1', 500);
    const card2 = stubCard('Sell', 'Action2', 'Share2', 1000);

    (component as any).cards = { a: card1, b: card2 };

    const spy1 = jest.spyOn(card1.get('search')!.valueChanges, 'subscribe');
    const spy2 = jest.spyOn(card2.get('search')!.valueChanges, 'subscribe');

    runNgOnChanges();

    expect(spy1).toHaveBeenCalledTimes(1);
    expect(spy2).toHaveBeenCalledTimes(1);
    expect((component as any).subscribedControls.has(card1.get('search')!)).toBe(true);
    expect((component as any).subscribedControls.has(card2.get('search')!)).toBe(true);
  });

  it('calls refreshDuplicateValidation when search value changes', () => {
    const card = stubCard('Buy', 'Action', 'Share', 500);
    (component as any).cards = { a: card };

    const refreshSpy = jest.spyOn(component as any, 'refreshDuplicateValidation');

    runNgOnChanges();

    card.get('search')!.setValue('abc');
    card.get('search')!.setValue('def');

    expect(refreshSpy).toHaveBeenCalledTimes(2);
  });

  it('stops reacting after destroy$ emits', () => {
    const card = stubCard('Buy', 'Action', 'Share', 500);
    (component as any).cards = { a: card };

    const refreshSpy = jest.spyOn(component as any, 'refreshDuplicateValidation');

    runNgOnChanges();
    card.get('search')!.setValue('first');
    expect(refreshSpy).toHaveBeenCalledTimes(1);

    (component as any).destroy$.next();
    (component as any).destroy$.complete?.();

    card.get('search')!.setValue('second');
    expect(refreshSpy).toHaveBeenCalledTimes(1);
  });

  it('ignores stubCards without a search control', () => {
    const noSearchCard = stubCard('Buy', 'Action', 'Share', 500);
    noSearchCard.removeControl('search'); // simulate missing control
    (component as any).cards = { a: noSearchCard };

    runNgOnChanges();

    expect((component as any).subscribedControls.size).toBe(0);
  });
});