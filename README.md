# Car-Price-Prediction-using-Linear-Regression

using System;
using System.Data;
using System.Data.SqlClient;
using System.Web.UI.WebControls;
using System.Configuration;
using System.Web.DynamicData;

namespace cascading_dropdownlist
{
    public partial class WebForm1 : System.Web.UI.Page
    {
        SqlConnection sqlcon = new SqlConnection(ConfigurationManager.ConnectionStrings["con"].ConnectionString);

        protected void Page_Load(object sender, EventArgs e)
        {
            sqlcon.Open();

            if (!IsPostBack)
            {
                BindCountry();
            }
            
        }

        private void BindCountry()
        {
            string query = "select country_id, country_nm from country";
            using (SqlCommand cmd = new SqlCommand(query, sqlcon))
            {
                SqlDataAdapter adapter = new SqlDataAdapter(cmd);
                DataTable dt = new DataTable();
                adapter.Fill(dt);

                DropDownList1.DataSource= dt;
                DropDownList1.DataTextField = "country_nm";
                DropDownList1.DataValueField="country_id";
                DropDownList1.DataBind();
            }
            DropDownList1.Items.Insert(0, "Please select country");
        }

        protected void DropDownList1_SelectedIndexChanged(object sender, EventArgs e)
        {
            BindState(int.Parse(DropDownList1.SelectedValue));
        }


        private void BindState(int country_id)
        {
            string query = "select state_id, state_nm from state where country_id = @value1";
            using (SqlCommand cmd = new SqlCommand(query,sqlcon))
            {
                cmd.Parameters.AddWithValue("@value1", country_id);
                
                SqlDataAdapter adapter = new SqlDataAdapter(cmd);
                DataTable dt = new DataTable();
                adapter.Fill(dt);

                DropDownList2.DataSource = dt;
                DropDownList2.DataTextField = "state_nm";
                DropDownList2.DataValueField = "state_id";
                DropDownList2.DataBind();
                
            }
            DropDownList2.Items.Insert(0,"Please select state");
        }

        protected void DropDownList2_SelectedIndexChanged(object sender, EventArgs e)
        {
            BindCity(int.Parse(DropDownList2.SelectedValue));
        }


        private void BindCity(int state_id)
        {
            string query = "select city_id, city_nm from city where state_id = @value1";
            using (SqlCommand cmd = new SqlCommand(query, sqlcon))
            {
                cmd.Parameters.AddWithValue("@value1", state_id);

                SqlDataAdapter adapter = new SqlDataAdapter(cmd);
                DataTable dt = new DataTable();
                adapter.Fill(dt);

                DropDownList3.DataSource = dt;
                DropDownList3.DataTextField = "city_nm";
                DropDownList3.DataValueField = "city_id";
                DropDownList3.DataBind();

            }
            DropDownList3.Items.Insert(0,"Please select city");
        }

        protected void DropDownList3_SelectedIndexChanged(object sender, EventArgs e)
        {

        }
        protected void Button1_Click(object sender, EventArgs e)
        {
            BindGridView(int.Parse(DropDownList3.SelectedValue));
        }


        private void BindGridView(int city_id)
        {
             string query = "select c.country_nm,s.state_nm,ci.city_nm " +
                   "from country as c " +   //c and " mdhe space pahije nahitr error etiy (error near as keyword).
                   "inner join state as s " +
                   "on c.country_id = s.country_id " +

                   "inner join city as ci " +
                   "on s.state_id=ci.state_id " +
                   "where ci.city_id = @value1";
                

            using (SqlCommand cmd = new SqlCommand(query,sqlcon))
            {
                cmd.Parameters.AddWithValue("@value1", city_id);

                SqlDataAdapter adapter = new SqlDataAdapter(cmd);
                DataTable dt = new DataTable();
                adapter.Fill(dt);
                    
                GridView1.DataSource = dt;
                GridView1.DataBind();
            }
        }
    }
}
