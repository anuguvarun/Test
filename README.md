// one-trade-confirm-message.component.spec.ts
import { TestBed } from '@angular/core/testing';
import { ChangeDetectionStrategy } from '@angular/core';
import { OneTradeConfirmMessageComponent } from './one-trade-confirm-message.component';

describe('OneTradeConfirmMessageComponent', () => {
  beforeEach(async () => {
    await TestBed.configureTestingModule({
      // If the component is standalone (most likely, since it uses `imports:`),
      // we can import it directly here.
      imports: [OneTradeConfirmMessageComponent],
    })
      // Avoid pulling in the external template and any of its dependencies
      .overrideComponent(OneTradeConfirmMessageComponent, {
        set: { template: '<div>stub</div>' },
      })
      .compileComponents();
  });

  it('should create', () => {
    const fixture = TestBed.createComponent(OneTradeConfirmMessageComponent);
    const component = fixture.componentInstance;
    expect(component).toBeTruthy();
  });

  it('should use the correct selector', () => {
    // Angular stores runtime metadata on ɵcmp
    const cmpDef: any = (OneTradeConfirmMessageComponent as any).ɵcmp;
    // selectors looks like: [[ 'fc-one-trade-confirm-message-ui' ]]
    expect(cmpDef.selectors?.[0]?.[0]).toBe('fc-one-trade-confirm-message-ui');
  });

  it('should use OnPush change detection', () => {
    const cmpDef: any = (OneTradeConfirmMessageComponent as any).ɵcmp;
    expect(cmpDef.changeDetection).toBe(ChangeDetectionStrategy.OnPush);
  });

  it('should be a standalone component (if applicable)', () => {
    const cmpDef: any = (OneTradeConfirmMessageComponent as any).ɵcmp;
    // Only true if the component was declared with `standalone: true`
    expect(cmpDef.standalone).toBe(true);
  });

  it('should render the (overridden) template without errors', () => {
    const fixture = TestBed.createComponent(OneTradeConfirmMessageComponent);
    fixture.detectChanges();
    expect(fixture.nativeElement.textContent).toContain('stub');
  });
});