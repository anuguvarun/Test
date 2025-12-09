-------
PostMapping("/{tradeOrderRequestId}/item-status")
    public ResponseEntity<Void> updateTradeItemStatus(
            @PathVariable("tradeOrderRequestId") long tradeOrderRequestId,
            @RequestBody @Valid TradeItemStatusUpdateRequest request) {

        log.debug("Received request to update item status for tradeOrderRequestId={} and itemId={}",
                tradeOrderRequestId, request.getItemId());

        // optional: ensure path id and body id match if body field is used
        if (request.getTradeOrderRequestId() != null
                && !request.getTradeOrderRequestId().equals(tradeOrderRequestId)) {
            throw new BusinessValidationException("tradeOrderRequestId in path and body must match");
        }

        // force the id from path to avoid mismatch
        request.setTradeOrderRequestId(tradeOrderRequestId);

        tradeOrderService.updateTradeItemStatus(request);

        // requirement: on valid request 200 and DB updated
        return new ResponseEntity<>(HttpStatus.OK);
    }
}