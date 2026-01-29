// ---------- UNAPPROVED (Needs Action) ----------
    @Query("""
        SELECT COUNT(DISTINCT tor.tradeRequestId)
        FROM TradeOrderRequestsCGF tor
        JOIN TradeOrderRequestItemsCGF tori
             ON tor.tradeRequestId = tori.tradeRequestId
        WHERE tor.reportStatus IS NULL
          AND tori.tradeStatusCode IN (:itemStatuses)
    """)
    Long getUnApprovedCount(
            @Param("itemStatuses") List<String> itemStatuses
    );

    // ---------- UNSETTLED ----------
    @Query("""
        SELECT COUNT(DISTINCT tor.tradeRequestId)
        FROM TradeOrderRequestsCGF tor
        JOIN TradeOrderRequestItemsCGF tori
             ON tor.tradeRequestId = tori.tradeRequestId
        WHERE tori.tradeStatusCode IN (:itemStatuses)
    """)
    Long getUnsettledCount(
            @Param("itemStatuses") List<String> itemStatuses
    );