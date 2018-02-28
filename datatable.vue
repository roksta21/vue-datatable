<template>
	<div>
		<div class="row">
            <div class="col-md-6">
            	<select class="form-control" style="width: 40%" v-model="show_rows">
            		<option value="10">Show 10</option>
            		<option value="25">Show 25</option>
            		<option value="50">Show 50</option>
            	</select>
            </div>
            <div class="col-md-6">
                <div class="form-group">
                    <input type="text" v-model="search_data" class="form-control" placeholder="Search">
                </div>
            </div>
            <div class="col-md-12">
            	<table class="table table-striped table-hover table-responsive">
            		<thead>
            			<tr>
            				<th v-for="column in columns" @click="sortBy(column)">{{ column }}</th>
            			</tr>
            		</thead>
            		<tbody>
            			<tr v-for="row in show_data" class="link" @click="goTo(row)">
            				<td v-for="i in columns_count">{{ row[columns[i - 1]] }}</td>
            			</tr>
            			<tr v-if="show_data_count < 1">
            				<td :colspan="columns_count">
            					<div class="alert alert-warning">No data</div>
            				</td>
            			</tr>
            		</tbody>
            	</table>
            </div>
            <div class="col-md-12">
            	<template v-if="current_page != 1">
            		<a href="#" class="btn btn-primary pull-left" @click.prevent="showData(current_page - 1)">Previous</a>
            	</template>
            	<template v-if="current_page < pages">
            		<a href="#" class="btn btn-primary pull-right" @click.prevent="showData(current_page + 1)">Next</a>
            	</template>
            	<p align="center">Page {{ current_page }} of {{ pages }}</p>
            </div>
        </div>
	</div>
</template>

<script>
	export default {
		mounted() {
			this.showData(1); 
		},

		data() {
			return {
				search_data: '',
				current_page: 1,
				show_rows: 10,
				sort_by: '',
				reverse_sort: false,
				filtered_data: []
			}
		},

		props: {
			mydata: {
				type: Array,
				default() {
					return [];
				}
			}
		},

		methods: {
			showData(page) {
				this.current_page = page;
			},

			sortBy(criteria) {
				if (criteria == this.sort_by) {
					this.reverse_sort = !this.reverse_sort;
				}

				this.sort_by = criteria;
				this.showData(this.current_page);
			},

			goTo(row) {
				this.$emit('rowClicked', row);
			}
		},

		computed: {
			columns() {
				if (this.mydata[0]) {
					return Object.keys(this.mydata[0]);
				} else {
					return {};
				}
			},

			columns_count() {
				return this.columns.length;
			},

			pages() {
				return Math.ceil(this.filtered_data.length / this.show_rows);
			},

			show_data() {
				let processed_data = [];

				if (this.reverse_sort) {
					processed_data = _.sortBy(this.filtered_data, this.sort_by).reverse();
				} else {
					processed_data = _.sortBy(this.filtered_data, this.sort_by);
				}

				return processed_data.filter((row, index) => {
                    return (((this.current_page - 1) * this.show_rows) < (index + 1)) && ((index + 1) <= (this.current_page * this.show_rows));
                });
			},

			show_data_count() {
				return this.show_data.length;
			}
		},

		watch: {
			mydata(newval, oldval) {
				this.filtered_data = this.mydata;
				this.showData(1);
			},

			search_data(newval, oldval) {
				this.current_page = 1;

				if (newval.length > 0) {
					this.filtered_data = this.mydata.filter(row => {
						return this.columns.filter(column_name => {  
							if (typeof(row[column_name]) == 'string') {
								return row[column_name].match(new RegExp(newval, 'gi'));
							}
						}).length > 0;
					});
				} else {
					this.filtered_data = this.mydata;
				}
			},

			show_rows(newval, oldval) {
				this.showData(1);
			}
		}
	}
</script>

<style>
	table thead th {
		text-transform: uppercase;
		cursor: pointer;
	}
</style>