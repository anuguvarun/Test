@PostMapping("/item-status")
    public ResponseEntity<Void> updateTradeItemStatus(
            @RequestBody TradeItemStatusUpdateRequest request) {

        tradeOrderService.updateTradeItemStatus(request);

        // If we reach here, request is valid and DB (later) is updated
        return ResponseEntity.ok().build();          // 200
    }
}