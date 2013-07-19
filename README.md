notes
=====

To do nested forms:

<%= form_for @user do |f| %>
<%= f.fields_for :posts do |x| %>
  <%= x.title %>
<%= end %>

inverse_of most useful when doing validations on nested data

