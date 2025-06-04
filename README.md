// shared/base/base-trade-card.component.ts

import { SecurityType, UnitType } from 'path-to-your-enums';

export abstract class BaseTradeCardComponent {
  tradeType = SecurityType;
  unitType = UnitType;

  index!: number;
  cards: any[] = [];

  get isFi() {
    return this.cards[this.index]?.value?.tradeType === SecurityType.FI;
  }

  getFeeOrLiquidateText(cardValue: any): string {
    if (!cardValue) {
      return '';
    }

    const isDollar = cardValue.shareToggle === UnitType.Dollars;
    const isTradeTypeValid = [SecurityType.ETF, SecurityType.MF].includes(cardValue.tradeType);

    if (isDollar && isTradeTypeValid) {
      return cardValue.feesIncluded ? 'Included, if applicable' : '';
    }

    return cardValue.liquidateAll ? 'Yes' : '';
  }
}