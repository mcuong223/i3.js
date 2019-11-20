### Paging C#
```csharp
    // hàm set data cho paging data (đã tạo rồi)
    private void SetPagingData<T>(PagingData pagingData, IQueryable<T> query)
    {
        pagingData.ItemTotal = query.Count();
        pagingData.PageTotal = pagingData.GetPageTotal();
        pagingData.PageIndex = pagingData.GetPageIndex();
        if (pagingData.PageSize.HasValue)
        {
            var skip = pagingData.GetSkipNumber();
            var take = pagingData.PageSize.Value;
            query = query.Skip(skip).Take(take);
        }
    }
    
    public Acknowledgement<DataWithPaging<Product>> GetProductListTest(SearchModel searchModel)
    {
            var ack = new Acknowledgement<DataWithPaging<Product>>();
            var db = POSReadOnlyContext;
            // Tạo queryable bình thường
            var query = db.Products.Include(i => i.ProductAmountThreshold)
                                          .Include(i => i.ProductUnit)
                                          .Include(i => i.ProductBatches.Select(j => j.Transactions))
                                          .AsQueryable();
            // filter theo search value
            var condition = $"Name.Contains(\"{searchModel.SearchValue}\")";
            query = System.Linq.Dynamic.DynamicQueryable.Where(query, condition);    
            
            // skip take paging
            SetPagingData(searchModel.PagingData, query);
            
            // lay data ve memory
            var data = query.ToList();
            ack.Data = new DataWithPaging<Product>()
            {
                Data = data,
                PagingData = searchModel.PagingData,
            };
            ack.IsSuccess = true;
            return ack;
    }
    
```


### Paging javascript

 ```jsx
   _getData = () => {
        const { searchModel, } = ~~
        ajax.post({
            url: `/api/xxx/xxx/`,
            data: JSON.stringify(searchModel),
            successCallback: ack => {
                const _data = ack.data.data // data đã paging
                const _pagingData = ack.data.pagingData // paging data đã được tính toán
                // update state ở đây
            }
       })
   }
    _gotoNext = () => {
        const { pagingData } = ~~
        if (pagingData.pageIndex < pagingData.pageTotal) {
            this._gotoPage(pagingData.pageIndex + 1);
        }
    }
    _gotoPrev = () => {
        const { pagingData } = ~~
        if (pagingData.pageIndex > 1) {
            this._gotoPage(pagingData.pageIndex - 1);
        }
    }
    _gotoPage = (index) => {
        const { searchModel, } = ~~
        this.updateObject(searchModel.pagingData, { pageIndex: index }, () => {
            this._getData();
        })
    }
    _gotoFirstPage = () => {
        this._gotoPage(1);
    }
    _gotoLastPage = () => {
        const { searchModel, } = ~~
        this._gotoPage(searchModel.pagingData.pageTotal);
    }
    import Pagination from 'components/Pagination';
    <Pagination
        total={pagingData.pageTotal}
        current={pagingData.pageIndex}
        onClickNumber={this._gotoPage}
        onClickNext={this._gotoNext}
        onClickPrev={this._gotoPrev}
        onClickFirst={this._gotoFirstPage}
        onClickLast={this._gotoLastPage}
    />
```
