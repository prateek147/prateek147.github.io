---
layout: post
title: "Pagination in Restkit 0.2 using RKPaginator"
date: 2013-06-21 21:30
comments: true
categories: [ios]
---

Restkit 0.2 supports pagination through a class called RKPaginator. It has all the necessary methods for supporting pagination properly and efficiently.

The first step is to define the properties required for pagination.

{% codeblock lang:objc %}
//Properties required for pagination
@property (nonatomic,strong) RKPaginator *paginator;
@property (nonatomic,strong) NSMutableArray *objects;
@property (nonatomic,assign) BOOL isPaginatorLoading;
{% endcodeblock %}

Also make sure to define the url path for your request. The parameters after <code>:</code> denotes the attributes of the RKPaginator object. For e.g, in the below path, instead of <code>:currentPage</code>, the value that will be passed is the <code>currentPage</code> attribute of the paginator object. The two attributes that are necessarily required are <code>perPage</code> and <code>currentPage</code>.

{% codeblock lang:objc %}
#define kUrlStringForPagination @"/ios-pagination/?dummy_variable=dummyVariable&page=:currentPage&per_page=:perPage&dummy_variable2=dummyVariable2"
{% endcodeblock %}

<!-- more --> 

Now, initialize the paginator and make the first request whenever you want with the following code.

{% codeblock lang:objc %}
// Create weak reference to self to use within the paginators completion block
__weak typeof(self) weakSelf = self;
  weakSelf.objects = [NSMutableArray array];
  // Setup paginator
  if (!self.paginator) {
    
    RKObjectManager *objectManager = [RKObjectManager sharedManager];
    NSString *requestString = kUrlStringForPagination;
    self.paginator = [objectManager paginatorWithPathPattern:requestString];
    self.paginator.perPage = 10; 
  
  //Show loader 
  //Code to show loader 


   //Set completion block for this paginator
    [self.paginator setCompletionBlockWithSuccess:^(RKPaginator *paginator, NSArray *objects, NSUInteger page) {
      weakSelf.isPaginatorLoading = NO;
      //Hide Loader 
      //Code to hide Loader
      
      if (page == 1) {
        [weakSelf.objects removeAllObjects];
       }
      [weakSelf.objects addObjectsFromArray:objects];
      NSLog(@"Loaded Objects are %@",weakSelf.objects);

      
  } failure:^(RKPaginator *paginator, NSError *error) {
     [MBProgressHUD hideHUDForView:weakSelf.view animated:YES];
      weakSelf.paginator = nil;
  }];
  }
  NSLog(@"Loaded Objects are %@",weakSelf.objects);
  [self.paginator loadPage:1];
{% endcodeblock %}

Now, put this code whenever you are interested to load the next page.

{% codeblock lang:objc %}
if([self.paginator hasNextPage]){
      NSLog(@"Loading next page");
      if(!self.isPaginatorLoading){
        self.isPaginatorLoading = YES;
        [self.paginator loadNextPage];
      }
  } 
{% endcodeblock %}

Also, make sure that the response descriptor is set for this call.

{% codeblock lang:objc %}
RKEntityMapping *userResponseMapping = [User restkitObjectMappingForStore:store];
  RKResponseDescriptor *setUserDescriptor =
  [RKResponseDescriptor responseDescriptorWithMapping:userResponseMapping
                                          pathPattern:@"/ios-pagination/"
                                              keyPath:nil
                                          statusCodes:successSet];
  [[RKObjectManager sharedManager] addResponseDescriptor:setUserDescriptor];
{% endcodeblock %}

Please feel free to ask any doubts in the comments sections below :)