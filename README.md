updateCounts(summary: TradeOrderSummaryResponse): void {

  this.tabs = this.tabs.map(tab => {

    switch (tab.id) {

      case 'approvals-needs-action':
        return {
          ...tab,
          title: `Needs action (${summary.unApprovedCount ?? 0})`
        };

      case 'approvals-order-flow-management':
        return {
          ...tab,
          title: `Order flow management (${summary.unsettledCount ?? 0})`
        };

      default:
        return tab;
    }

  });

}