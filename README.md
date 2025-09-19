// check-held-securities.spec.ts
import { jest } from '@jest/globals';
// If you have real enums/types, import them instead of stubbing:
enum ModalType { default = 'default' }
enum ButtonType { primary = 'primary' }
// Import the class under test:
import { AccountComponent } from './account.component';
// And the component used inside the modal:
import { AccountHeldSecuritiesComponent } from './account-held-securities.component';

describe('AccountComponent.checkHeldSecurities', () => {
  let component: AccountComponent;
  let modalService: { open: jest.Mock };

  beforeEach(() => {
    modalService = { open: jest.fn() } as any;
    // @ts-expect-error – construct with only what we need for this unit
    component = new AccountComponent(modalService);
  });

  it('opens the Held Securities modal with the expected config', () => {
    const modalRef = { componentInstance: {} } as any;
    modalService.open.mockReturnValue(modalRef);

    component.accountPositions = { accountPositions: [{ id: 1 }] };

    const returned = component.checkHeldSecurities();

    expect(modalService.open).toHaveBeenCalledTimes(1);
    expect(modalService.open).toHaveBeenCalledWith(
      expect.objectContaining({
        windowId: 'heldSecuritiesModal',
        noDismiss: false,
        type: ModalType.default, // if your code uses a real enum, this will match
        title: 'Held securities',
        component: AccountHeldSecuritiesComponent,
        size: 'lg',
        analyticsConfig: { name: 'DF Held securities modal' },
        buttons: [
          expect.objectContaining({
            text: 'Done',
            buttonId: 'confirm-button',
            type: ButtonType.primary,
            accessibilityConfig: { label: 'Held securities modal' },
            action: 'dismiss',
          }),
        ],
      }),
    );

    // returns modalRef
    expect(returned).toBe(modalRef);
  });

  it('passes accountPositions into the modal component instance when present', () => {
    const modalRef = { componentInstance: {} } as any;
    modalService.open.mockReturnValue(modalRef);

    const positions = [{ id: 'abc' }, { id: 'def' }];
    component.accountPositions = { accountPositions: positions };

    component.checkHeldSecurities();

    expect(modalRef.componentInstance.accountPositions).toBe(positions);
  });

  it('leaves componentInstance.accountPositions undefined when component.accountPositions is absent', () => {
    const modalRef = { componentInstance: {} } as any;
    modalService.open.mockReturnValue(modalRef);

    component.accountPositions = undefined;

    component.checkHeldSecurities();

    expect(modalRef.componentInstance.accountPositions).toBeUndefined();
  });

  it('handles a falsy modalRef from modalService.open()', () => {
    modalService.open.mockReturnValue(null);

    const returned = component.checkHeldSecurities();

    expect(returned).toBeNull();
    // and of course no attempt to touch componentInstance
    // (no assertion needed since touching it would throw)
  });
});